---
layout: post
title:  "Dynamic Programming"
date:   2019-10-12 19:19:21 +0700
tech: true
---

So everyone who ever lived and learned algorithms, at some point in their journey, excitedly typed out a "how dynamic programming works" blog post. Ladies and Gentleman, this is not it. Instead, this is simply just some fun with python and proving to myself that this dynamic programming stuff is cool and useful. If you are looking for a how to on dynamic programming, I would totally recommend [The Algorithm Design Manual](http://www.algorist.com/)!

![Cover of The Algorithm Design Manual](http://www.algorist.com/images/adm2cover.jpg)                                                      
I am working through this book in preparation for job interviews and really love the style and content of this book -- as I still work through parts of the bible ([CLRS](https://en.wikipedia.org/wiki/Introduction_to_Algorithms)), I find this book a sort of "abridged" version more or less perfect for someone interested in learning more than you can ever learn grinding away on LeetCode, but still not too off in theory land.

***

# Fibonacci and Dynamic Programming

This is the dynamic programming example everyone uses... it has almost been beaten to death imo. However, it honestlt took me a few times to really "get it", to understand what is really going on here and how it relates to the concept of dynamic programming as a whole. To solidfy my understanding of how fibonacci can be sped up with dynamic programming, I coded up four solutions, from worst to best.

### Solution 1: Recursion
You're typical solution that overflows the stack and runs slow.
```
def fib(n):
    if n < 2:
        return n

return fib(n-1) + fib(n-2)
```

### Solution 2: Recursion and Memoization
The optimized solution that still overflows the stack.
```
def fib(n):
    if n in seen:
        return seen[n]

    seen[n-1], seen[n-2] = fib(n-1), fib(n-2)
    return seen[n-1] + seen[n-2]
```

### Solution 3: Iteration and Linear Space Memoization
The optimized, stack friendly solution most people are happy with.
```
def fib(n):
    dp = [0] * (n+1)
    dp[1] = 1

    for i in range(2,n+1):
        dp[i] = dp[i-1] + dp[i-2]

    return dp[n]
```


### Solution 4: Iteration and Constant Space Memoization
The best, A+ solution.
```
def bestFib(n):
    l, ll = 0, 1

    for i in range(2, n+1):
        l, ll = ll, ll + l

    return ll
```

## So how do these all compare (using an input of 40)?
**Solution 1:** *300,000,000+* function calls
**Solution 2:** *81* function calls
**Solution 3:** *4* function calls
**Solution 4:** *4* function calls

Don't believe me?!? See below!

![Comparison of profiling of algorithms](https://imgur.com/Htf8d5P.png)

## And what is the damage done by using 3 instead of 4?
Jumping into some profiling fun, I installed the [guppy](https://pypi.org/project/guppy/) library. In doing so, I was able to see the size of the heaps of each respective algorithm after it terminated. It turns out, on sufficiently large data sets, it is incredibly substantial (obvious I know). For example, I saw a 100x difference in memory usage, which resulted in a 10x slow down from 3 to 4 with a large `n` value. Pretty crazy (yet totally expected...)!


