---
layout: post  
title:  "Ruby Deep Dive I: How Ruby Executes Code"
date:   2022-03-25 00:00:00 +0700   
tech: true
---

After work, I have found myself poking around the [Ruby codebase (which is open source)](https://github.com/ruby/ruby). One thing I particularly enjoy when working in C# at DocuSign is [diving into the source code, available via the source code browser here](https://source.dot.net/) (*does Ruby have one of these? it should.*).

There were times when I was diving into a particular aspect of the language, how async worked, the differences between the synchronization primitives, etc. and a thorough understanding required a peek into the internals. Thus, upon discovery of the Ruby source code on GitHub I was thrilled.

I spent some time while at Colby College diving into programming language things. Most notably I hacked together a [somewhat functioning programming language called Sailfish](https://github.com/sailfish-lang/sailfishc) and [developed a novel algorithm for discovering asynchronous method inputs for JavaScript](https://www.franktip.org/pubs/icse2022-nessie.pdf). 

![big brain on brad](https://media.giphy.com/media/l2YWoKTYhYyuJgA5G/giphy.gif)

Ok, *novel* was what the crew called my algorithm in the first draft of the paper... which was rejected. In the second draft, which was ultimately the accepted version, they put *unsound*.

Thus, programming languages have been a lingering interest for a few years at this point (I'd say probably since [RustConf 2018](https://2018.rustconf.com/)? or maybe since I binge listened to [the New Rustacean](https://newrustacean.com/)?). Having the opportunity to actually work on the same team as Graydon Hoare during my 2019 summer internship at the Stellar Development Foundation was definitely a huge plus. Anyway, I digress.

While the ultimate goal here is to learn more about Ruby, I have no idea where this source code exploration adventure will take me. Maybe I will hack on my own VM? Maybe I will try to develop an async debug console ([check out Tokio's, it is super neat!](https://github.com/tokio-rs/console)). Maybe I will replicate [Coyote](https://www.microsoft.com/en-us/research/project/coyote/), but for Ruby? Who knows?

Let's get to hacking.

![hacking gif](https://media.giphy.com/media/LcfBYS8BKhCvK/giphy.gif)

# How Ruby Executes Code

Answering the question *how Ruby Executes* is the ultimate goal of the first post in this series. Our journey starts in [main.c](https://github.com/ruby/ruby/blob/master/main.c) with a good old main method.


```c
int main(int argc, char **argv) {
  /* shortened for brevity and sanity */
  return rb_main(argc, argv);
}
```

*There is an optimization if we have WASM or EMSCRIPTEN. We ignore it here.* Thus, we end up here:
```c
int
rb_main(int argc, char **argv)
{
  /* shortened for brevity and sanity */

  ruby_sysinit(&argc, &argv);
  {
    RUBY_INIT_STACK;
    ruby_init();
    return ruby_run_node(ruby_options(argc, argv));
  }
}
```

### Initializing Ruby

[ruby_sysinit](https://github.com/ruby/ruby/blob/a892e5599ec8ec441a8d8b878efa855ef283ed08/ruby.c#L2691) grabs some args and then [ruby_init()](https://github.com/ruby/ruby/blob/5f10bd634fb6ae8f74a4ea730176233b0ca96954/eval.c#L65) does some setup work. An interesting line here is:

```c
if (GET_VM())
	return 0;
```

where [`GET_VM()` is defined as](https://github.com/ruby/ruby/blob/master/vm_core.h#L1809):

```c
#define GET_VM() rb_current_vm()
```

and finally [rb_current_vm()](https://github.com/ruby/ruby/blob/master/vm_core.h#L1881):

```c
static inline rb_vm_t *
rb_current_vm(void)
{
    /* shortened for brevity and sanity */
    return ruby_current_vm_ptr;
}
```

which is actually set as part of [Init_BareVM](https://github.com/ruby/ruby/blob/343ea9967e4a6b279eed6bd8e81ad0bdc747f254/vm.c#L3961)

**Note to self**: if I want to eventually point to my own VM, this is where I'd do it.

### Executing Ruby 

Jumping back to where we were in the main method, we next look to where the code is actually executed:

```c
int
rb_main(int argc, char **argv)
{
  /* shortened for brevity and sanity */

  ruby_sysinit(&argc, &argv);
  {
    RUBY_INIT_STACK;
    ruby_init();
    return ruby_run_node(ruby_options(argc, argv)); <---- WE ARE HERE ----
  }
}
```

Digging through the source code we see this calls `ruby_run_node` which in turn calls `ruby_exec_node` which calls `rb_ec_exec_node` and eventually we get to [rb_iseq_eval_main](https://github.com/ruby/ruby/blob/343ea9967e4a6b279eed6bd8e81ad0bdc747f254/vm.c#L2561) (seen below).

```c
VALUE
rb_iseq_eval_main(const rb_iseq_t *iseq)
{
    /* shortened for brevity and sanity */

    val = vm_exec(ec, true);
    return val;
}
```

So at this point, we are executing our instruction sequence (instruction set/bytecode) on the VM. Woohoo!

But wait!!! Where did this instruction sequence come from?

![but wait!](https://media.giphy.com/media/DhZSDMnqPu20U/giphy.gif)

Stepping back in the code, this instruction sequence was generated as part of `ruby_options(argc, argv)` which we see is passed to `ruby_run_node` above in the `rb_main` method. Investigating, we see how [ruby_options](https://github.com/ruby/ruby/blob/5f10bd634fb6ae8f74a4ea730176233b0ca96954/eval.c#L109) is defined:

```c
void *
ruby_options(int argc, char **argv)
{
   /* shortened for brevity and sanity */

  void *volatile iseq = 0;

  ruby_init_stack((void *)&iseq);
  EC_PUSH_TAG(ec);
  if ((state = EC_EXEC_TAG()) == TAG_NONE) {
    SAVE_ROOT_JMPBUF(GET_THREAD(), iseq ruby_process_options(argc, argv)); <--- Look here ---
  }
  else {
    /* shortened for brevity and sanity */
  }
  EC_POP_TAG();
  return iseq;
}
```

This eventually gets us to [process_options](https://github.com/ruby/ruby/blob/a892e5599ec8ec441a8d8b878efa855ef283ed08/ruby.c#L1720) which takes in the command line options as well.
Here we do a lot of things. A few things stuck out to me. In the method linked [here](https://github.com/ruby/ruby/blob/a892e5599ec8ec441a8d8b878efa855ef283ed08/ruby.c#L1720) we:
* [create a new parser](https://github.com/ruby/ruby/blob/a892e5599ec8ec441a8d8b878efa855ef283ed08/ruby.c#L1909)
* [parse/compile ast](https://github.com/ruby/ruby/blob/a892e5599ec8ec441a8d8b878efa855ef283ed08/ruby.c#L2029) and [here](https://github.com/ruby/ruby/blob/a892e5599ec8ec441a8d8b878efa855ef283ed08/ruby.c#L2034) as well depending on a few things I glossed over...
* [get the instruction](https://github.com/ruby/ruby/blob/a892e5599ec8ec441a8d8b878efa855ef283ed08/ruby.c#L2108)
* [dump instructions, assuming this is --dump=insns](https://github.com/ruby/ruby/blob/a892e5599ec8ec441a8d8b878efa855ef283ed08/ruby.c#L2113)

Fascinating!

# Conclusion

In this post, we discovered the basics of what happens when you run `ruby <FILEPATH>`.

1. ruby initializes, including initializing the stack
2. the cli options are parsed
3. the code is parsed and an AST is generated
4. the AST is walked and bytecode (instruction sequence) is generated
5. the instruction sequence is passed to the vm
6. the vm executes this code

By understanding this process, we discovered where we could dig deeper to learn more about each compilation step and even where we could insert our own VM.

Hope you enjoyed this post and learned a thing or two (besides how aweful C macros are). On to the next one!
