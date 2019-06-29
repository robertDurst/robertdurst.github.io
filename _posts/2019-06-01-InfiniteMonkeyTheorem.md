---
title: Infinite Monkey Theorem
updated: 2019-06-01 12:00
---

![Monkey dancing](https://media.giphy.com/media/dchERAZ73GvOE/giphy.gif)

### Infinite Monkey Theorem

In doing basic research for my first task at the Stellar Development Foundation, I came across the *Infinite Monkey Theorem*. The theorem states:

*"a monkey hitting keys at random on a typewriter keyboard for an infinite amount of time will almost surely type any given text, such as the complete works of William Shakespeare"* -- [Wikipedia](https://en.wikipedia.org/wiki/Infinite_monkey_theorem#References).

Believe it or not, this theorem is very much real and even has a nice mathematical proof.

#### Proof:
Consider the word `banana`. The odds that a single monkey types the word banana is, given a keyboard with **50** keys is `1/50`. Thus, the odds of a monkey typing, at random, the word banana on the first try is `(1/50) x (1/50) x (1/50) x (1/50) x (1/50) x (1/50) = 1/15,625,000,000` since these are all indepedent events. Consider then the odds that the monkey does not type teh word banana on its first try: `1 - 1/15,625,000,000`. So, we get that the odds of a monkey not typing banana on its first try to be `0.999999999936`. Consider a zoo of ten monkeys. If the zoo sat these ten monkeys down at ten desks and had them indepdently type six characters, the odds that none of the monkeys typed banana is:
```
0.999999999936 + ... + 0.999999999936 = (1 - (50 ^ 6)) ^ 10
                                      = 0.9999999993599997
```

Thus, as you get more and more monkeys, the probability of one of none of them typing banana on the first try approaches zero; at around 100,000,000,000 monkeys, the odds is: `0.0016615527035833107`.

So, in the case of a single monkey at a single keyboard, given an infinite number of keystrokes and an infinite amount of time, the odds that it types *banana* is almost gauranteed.

#### Takeaways

This thereom has two very interesting takeaways in the context of fuzzing:

1. Given enough time (and this may be a LOT of time), almost all inputs will eventually be reached
2. Given no direction, the odds of reaching all inputs in any reasonable amount of time is incredibly small

Thus, in implementing fuzzing for Stellar, I will need to use a fuzzer that is a bit smarter than a monkey and I will need to give it enough directions so it doesn't *bang away* down nonsensical paths, investigating non-meaningful things.