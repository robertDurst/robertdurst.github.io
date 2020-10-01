---
layout: post  
title:  "Full Async in C# and When Working From Home"
date:   2020-09-30 19:19:21 +0700   
tech: true
--- 

This past week I spent some time learning about Async-Await in C#. **TLDR** it *can be* a fascinating way to get more bang for your buck in terms of raw computing power if you're in a situation where you're IO bound. WOAH! What does that even mean? Well it turns out, if you're also working from home, you may be experiencing this phenomenon in real life. Let me explain...

## Working From Home - or as the cool kids say "WFH"

![Working From Home GIF](https://media.giphy.com/media/mCRJDo24UvJMA/giphy.gif)

As a new day begins, you'll likely sit down (or stand) at your desk with a slip of paper from yesterday outlining a list of TODO items for the day. It may look like this:
1. call Jim
2. email Alice
3. fix the *register* button, customers say it's been broken for a while
4. respond to Pete's code review

Maybe you spend some time dreading 1 so you move to 2, after all, you have a crush on Alice. However, let's pretend you're more robotic and just go down the list in order - everything is the same priority, first-in-first-out.

After three rings, you're put on hold. Jim's poor selection of hold music is put on and since you don't have [Google Assistant's new *Hold For Me* feature](https://blog.google/products/pixel/hold-for-me/) you end up sitting there, extremely unproductive; you're progress has been blocked. 

![On hold GIF](https://media.giphy.com/media/xUOwGleO7TBTy8rNvi/giphy.gif)

Realizing this is a huge waste of time to sit there and do nothing, you put Jim's hold music on speaker phone and boot up your laptop. Laptop open and on, you pull up your Outlook email client only to watch the loading icon go around and round - [must be another Microsoft Outage](https://www.theguardian.com/technology/2020/sep/29/major-microsoft-outage-brings-down-office-365-outlook-and-teams). 


![Loading Outlook](https://docs.microsoft.com/en-us/outlook/troubleshoot/client/performance/media/outlook-stops-at-the-loading-profile-screen/loading-profile.jpg)

With your email loading and the phone on hold, you continue in your quest to be productive. Leafing through your notes - handwritten notes, you're not in college anymore, come-on - you find a thrice starred, twice underlined and boldly circled note "delete line 38." Ah you did write-down the register button fix. As you delete line 38 and submit the change, Jim picks up the phone. Turns out Jim just wants to point out the register button is still not fixed... what a waste of time. Lowering the phone in frustration you see Bill Gates has personally restarted Microsoft Azure and your inbox is functional again. Quickly sending a Slack message to Jim to *"check the latest code changes ;)"*, you press send on that email you drafted to Alice. 

21 minutes into the day and you're more productive than ever - no meetings to slow you down. No in-person small talk to distract you. No slow elevator rides, re-filling the coffee pot, unjamming the printer, telling Jim to turn down his headphones, peaking at Alice to see if she notices you... nope! It's just you at your desk the multi-tasking, asynchronous machine!

![I'm the Machine GIF](https://media.giphy.com/media/QZbfyOXoicsz2RkezY/giphy.gif)

It turns out asynchronous communication and action, the switching between things instead of doing one thing at a time, can make a single person more productive. Isn't that why we are all so productive at home? Void of real-time interactions, we can optimize our time away by continuously keeping multiple things progressing at once, focusing on tasks we can progress forward and moving on when it becomes blocked (yes if you are in Zoom meetings all day - first off I empathize!!! - this won't apply to you). Sure it gets hard at times to context switch, resuming some work and remembering just what you were in the middle of doing. However, since the costs of waiting for things like email clients to load, phones to pick up (a.k.a. IO) outweigh the costs of remembering where you were, this method of work is much preferred.

## When It All Comes to a Halt

Smooth sailing into lunch, you make note of the last two items on your list. Ecstatic about the prospect of an early weekend, you warm-up yesterday's leftovers and return to your standing desk. As you hit the button and wait for the desk to lower, an impending sense of dread creeps over your covid-19 "I stay inside all day because it's safe not because I am lazy" *dad-bod*.

![Doh GIF](https://media.giphy.com/media/xT5LMLMPdRh2VRNVLi/giphy.gif)

The remaining items on your list include:
1. wait for password from Ivan to remote machine and delete a file
2. tell Ivan file has been deleted

Ivan from IT is easy to get a hold of, but once you begin a conversation with him, it's like [Slack and takes 100% of your CPU](https://medium.com/@matt.at.ably/wheres-all-my-cpu-and-memory-gone-the-answer-slack-9e5c39207cab). Furthermore, the key is ephemeral and is invalid once you exit the conversation *"for safety purposes."* How are you going to delete the file and then tell Ivan if you cannot exit the chat window?

Here we have a classic case of asynchronous deadlock: a single synchronous process, the conversation window with Ivan cannot finish because you need to tell him you deleted the file but cannot go on because you need to exit the conversation to access the remote machine. 

**Async-Await in C# has this same exact problem:**

*"Consider a library method that uses await on the result of some network download. You invoke this method and synchronously block waiting for it to complete... Now consider what happens if your invocation of it happens when the current SynchronizationContext is one that limits the number of operations that can be running on it to 1... So you invoke the method on that one thread and then block it waiting for the operation to complete. The operation kicks off the network download and awaits it. Since by default awaiting a Task will capture the current SynchronizationContext, it does so, and when the network download completes, it queues back to the SynchronizationContext the callback that will invoke the remainder of the operation. But the only thread that can process the queued callback is currently blocked by your code blocking waiting on the operation to complete. And that operation won’t complete until the callback is processed. Deadlock! "* - [Stephen Toub - ConfigureAwait FAQ](https://devblogs.microsoft.com/dotnet/configureawait-faq/#why-would-i-want-to-use-configureawaitfalse)

## The Solution to Async-WFH and Async-Await C# Happiness

As aptly put by David Fowler:

*"Once you go async, all of your callers SHOULD be async, since efforts to be async amount to nothing unless the entire callstack is async. In many cases, being partially async can be worse than being entirely synchronous. Therefore it is best to go all in, and make everything async at once."* - [Async Guidance](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#asynchrony-is-viral)

In order to properly function asynchronously in the world of C# or in this crazy world of WFH, avoid synchronous blockers at all costs! While these eat up your precious time and leave you staring at your never-ending list of TODO's, they limit you to functioning at just a fraction of your full capacity. Want to get more bang for your buck in terms of raw computing power AND spend your time doing more than waiting, consider an ALL async workflow!