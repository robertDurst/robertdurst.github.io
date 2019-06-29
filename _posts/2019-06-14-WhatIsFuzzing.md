---
title: What is Fuzzing?
updated: 2019-06-14 12:00
---

Ok, so it's been about three weeks since I started at Stellar and it's time for that *wtf is fuzzing* post.

## Motivation

To understand why fuzzing is interesting and useful, consider the following facts of life:

* everyone will die at some point
* humans cannot really act randomly
* unit testing kinda sucks

No matter how many unit tests you write, unless you are working on some incredibly limited, minute, simple project, you likely forget to test an edge case. How serious is this untested edge case? I don't know. It could be an embarrassing mishap you joke about over a post-lunch coffee walk... or it could be the heartbleed bug. Ouch! So what do you do? *Write more unit tests of course*. NO. You will never write enough unit tests. There will always be some crazy, non-sensical input you forget, some bit flip you didn't consider. Given time is limited and humans cannot actually act randomly, maybe we should find some way to automate the creation and execution of tests 🤔.

## Fuzz Testing

Fuzz testing is a test strategy that performs automated binary executions with randomly generated/mutated inputs, monitoring for program crashes. Conceptually, it is pretty simple:

1. Feed fuzzer an initial corpus of test cases
2. Fuzzer derives more inputs utilizing a genetic algorithm and feedback from previous tests
3. Feed inputs into program
4. Repeat
