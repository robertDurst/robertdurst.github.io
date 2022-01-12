---
layout: post  
title:  "Microsoft's Coyote vs. Async Server: Repro'ing Race Condition & Generating Test Corpus to Prove Fix"
date:   2022-01-12 19:19:21 +0700   
tech: true
---

A few months ago at DocuSign, I was part of an effort to make some server code fully asynchronous. We'd gone down the async rabbit hole and during load testing, witnessed throughput limited by an async deadlock induced bottleneck. This was the final step to full asynchrony for a dependent service (SPOA, my primary focus at the time). While a coworker wrote the first 98% of the PR to make the server fully async, I was tasked with bringing the changes over the line.

![checkered flag](https://media.giphy.com/media/VQ77RNKX0nyaA/giphy.gif)

In doing so, I ran into some challenges, the most devious being an interesting race condition in the server's graceful close behavior due to Linq querying it's state to wait on a collection of Tasks while the data structure itself is still writable.

### Some Psuedocode

```
State:
	- ConcurrentDictionary<Connection_Handler_ID, Task>

Connection_Acceptance:
	1. Accept the connection
	2. Handle connection asynchronously and add Task to ConcurrentDictionary

Connection_Acceptance_Loop (THREAD 1):
	1. while (true) { Connection_Acceptance }

Close (THREAD 2):
	1. Stop TCP Listener
	2. Get state from ConcurrentDictionary
	3. Wait for Tasks from ConcurrentDictionary to complete
```

### The Problem

Due to Close and Connection_Acceptance_Loop being executed on separate threads, it's possible to end up with the following execution sequence:

1. Call Close
2. Accept a new connection
3. Stop TCP Listener
4. Get state from ConcurrentDictionary
5. Handle Connection Async and add Task to ConcurrentDictionary
6. Wait for Tasks from ConcurrentDictionary to complete

**Bug:** *here we end up not waiting for the task handled in (5) to complete since we grabbed a snap shot of the state from the ConcurrentDictionary in (4)*

Conceptually its not too hard to come up with a solution. However, getting it right took a couple tries at finding the right synchronization primitive and was somewhat complicated by our desire to keep the main code path lockless.

While I could try to simulate all the possible states in my head or on paper, I came across a really cool Microsoft research project called [Coyoyte](https://www.microsoft.com/en-us/research/project/coyote/). Coyote does exactly this, simulates all (or at least many) of the possible code execution sequences, saving the executed sequences in order to be able to repro interesting ones on, say, failure.

Thus, instead of just proving to myself and code reviewers that my solution made sense, I could intentionally create the failure scenario then run my solution against it to make sure it worked.

### Setup - Hello World

While this tool sounds great in theory, I had no idea how to make it work. So, I started with a minimally useful hello world example to make sure everything is functioning.

```
// Program.cs
public class Public
{
  public static void Main(string[] args) { }

  [Microsoft.Coyote.SystematicTesting.Test]
  public static void Foo()
  {
    NUnit.Framework.Assert.AreEqual(1, 1);
  }
}
```

```
// Project.csproj
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.1</TargetFramework>
	<OutputType>exe</OutputType>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.Coyote" Version="1.2.8" />
    <PackageReference Include="Microsoft.Coyote.Test" Version="1.2.8" />
    <PackageReference Include="NUnit" Version="3.13.2" />
  </ItemGroup>

</Project>
```

*Testing with Coyote:*

```
dotnet publish -r win-x64 -c Release -p:PublishSingleFile=true --self-contained true
coyote rewrite .\bin\Release\netcoreapp3.1\win-x64\some_project.dll
coyote test .\bin\Release\netcoreapp3.1\win-x64\some_project.dll -m Foo -i 100
```

By packaging as a single file we get the .dll with all dependencies included/linked. `-m` here is method and `-i` here is iterations.

### Setup - Async Server

Unfortunately there are some limitations we must work around to use Coyote:

* forced to mock TCP connect
* cannot use SemaphoreSlim (part of testing code in Core/Core)
* unable to Sleep.Thread (hacky but would have been helpful in mocking)

So, I boiled down the behavior to a minimum example that hopefully captures most of the interesting logic at play.

### Coyote to the Rescue!

With an understanding of Coyote and a mocked async server, I sought to repro the ConcurrentDictionary LINQ query race. To do so, I created the following test method:

```
[Microsoft.Coyote.SystematicTesting.Test]
public static async System.Threading.Tasks.Task Foo()
{
    var expected = 0;

    MockServer server = new MockServer(() => Interlocked.Increment(ref expected));

    server.Open();

    server.Connect();
    server.Connect();
    server.Connect();

    var countTask = server.CloseAsync();

    server.Connect();
    server.Connect();
    server.Connect();

    var count = await countTask;

    Assert.IsTrue(expected <= count);
}
```

Here I connect 3 times before calling close and 3 times after. Since I don't delete connections from the dictionary (if a connection is finished and we await on it, it's just some extra CPU cycles), I'd expect the number of handled connections to be less than the number of connections we seek to await on.

intentionally using faulty logic, I get the following:

```
. Testing .\bin\Release\netcoreapp3.1\win-x64\test_test_shit.dll
... Method Foo
... Started the testing task scheduler (process:33436).
... Created '1' testing task (process:33436).
... Task 0 is using 'random' strategy (seed:1792470991).
... Telemetry is enabled, see http://aka.ms/coyote-telemetry.
..... Iteration #1
..... Iteration #2
..... Iteration #3
..... Iteration #4
..... Iteration #5
..... Iteration #6
..... Iteration #7
..... Iteration #8
..... Iteration #9
..... Iteration #10
..... Iteration #20
..... Iteration #30
..... Iteration #40
... Task 0 found a bug.
... Emitting task 0 traces:
..... Writing .\bin\Release\netcoreapp3.1\win-x64\Output\test_test_shit.dll\CoyoteOutput\test_test_shit_0_12.txt
..... Writing .\bin\Release\netcoreapp3.1\win-x64\Output\test_test_shit.dll\CoyoteOutput\test_test_shit_0_12.schedule
... Elapsed 2.3007777 sec.
... Testing statistics:
..... Found 1 bug.
... Scheduling statistics:
..... Explored 45 schedules: 45 fair and 0 unfair.
..... Found 2.22% buggy schedules.
..... Number of scheduling points in fair terminating schedules: 6 (min), 20 (avg), 66 (max).
... Elapsed 3.9781934 sec.
```

Investigating the cause of the bug I see:

```
<TelemetryLog> Telemetry is enabled, see http://aka.ms/coyote-telemetry.
<TestLog> Running test 'Public.Foo'.
<ErrorLog> Unhandled exception. NUnit.Framework.AssertionException:   Expected: True
  But was:  False

   at NUnit.Framework.Assert.ReportFailure(String message)
   at NUnit.Framework.Assert.ReportFailure(ConstraintResult result, String message, Object[] args)
   at NUnit.Framework.Assert.That[TActual](TActual actual, IResolveConstraint expression, String message, Object[] args)
   at NUnit.Framework.Assert.IsTrue(Boolean condition)
   at Public.Foo() in C:\Users\Robert.Durst\Desktop\test_test_shit\UnitTest1.cs:line 148
<StackTrace>    at System.Threading.Tasks.Task.InnerInvoke()
   at System.Threading.Tasks.Task.<>c.<.cctor>b__274_0(Object obj)
   at System.Threading.ExecutionContext.RunFromThreadPoolDispatchLoop(Thread threadPoolThread, ExecutionContext executionContext, ContextCallback callback, Object state)
   at System.Threading.Tasks.Task.ExecuteWithThreadLocal(Task& currentTaskSlot, Thread threadPoolThread)
   at System.Threading.Tasks.Task.ExecuteEntryUnsafe(Thread threadPoolThread)
   at System.Threading.Tasks.Task.ExecuteFromThreadPool(Thread threadPoolThread)
   at System.Threading.ThreadPoolWorkQueue.Dispatch()
   at System.Threading._ThreadPoolWaitCallback.PerformWaitCallback()


<StrategyLog> Found bug using 'random' strategy.
<StrategyLog> Testing statistics:
<StrategyLog> Found 1 bug.
<StrategyLog> Scheduling statistics:
<StrategyLog> Explored 45 schedules: 45 fair and 0 unfair.
<StrategyLog> Found 2.22% buggy schedules.
<StrategyLog> Number of scheduling points in fair terminating schedules: 6 (min), 20 (avg), 66 (max).
```

This means that we handled more requests than we were waiting for!

**With the scenario I was interested in repro'd, I now had a corpus of tests to prove my solution against.**

## Conclusion

While it was somewhat trivial to map out all the interesting execution sequences for this code, it was very helpful to test my hypothesis and confirm the solution worked against more rigorous testing. Coyote is actually a fairly easy to use tool and I didn't even use it very rigorously or test out its feature set to the fullest (can open failing tests in the Visual Studio Debugger). One take away for sure is that concurrency code is hard to get correct - it took more hours than I'd like to admit to get a reasonable mock working. If I come across some non-trivial concurrency code with few dependencies that I'd have to mock, I won't second guess taking Coyote out for a second spin.
