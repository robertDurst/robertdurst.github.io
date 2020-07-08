---
layout: post
title:  "Adventures in PL: Inferring Function Signatures in JavaScript"
date:   2019-10-21 19:19:21 +0700
tech: true
---


Last Friday morning, I sat in my small single in [Pierce dormitrory](https://www.colby.edu/visitors/location/pierce/) staring at the [TypeScript](https://www.typescriptlang.org/) compiler plugin I had just hacked together ([with some help of course](https://github.com/yunabe/tsapi-completions/blob/a8020c20d5d2235c2443f34688d30c56eb9d5aad/src/completions.spec.ts)). Given a small code snippet of TypeScript:

```typescript
import { map } from 'lodash';
map()
```

my plugin returned a list of type suggestions for the given function, much like what happens when you hover your mouse over a function in [VsCode's Intellisense](https://code.visualstudio.com/docs/editor/intellisense). It turns out these type suggestions themselves were actually not necesarilly types... as this depended on the type annotation header being used. For example, for the above code snippet, I get something along the lines of this:

```bash
{
  name: 'collection',
  documentation: [ { text: 'The collection to iterate over.', kind: 'text' } ],
  displayParts: [...],
  isOptional: false
}
{
  name: 'iteratee',
  documentation: [ { text: 'The function invoked per iteration.', kind: 'text' } ],
  displayParts: [...],
  isOptional: false
}
```

Which really did not help a whole lot. So, as I checked the clock and saw it was now a staggering 5AM, I came to wonder...

![how did I get here?](https://media.giphy.com/media/xT5LMK1iAmnCcOcOwU/giphy.gif)

***

# Inferring Callback Argument Positions in JavaScript

In investigating the extension of [a tool for generating feedback-directed, random tests for JavaScript](http://software-lab.org/publications/oopsla2018_LambdaTester.pdf), I spent some time experimenting with the tool's callback position inference algorithm. For the less informed, this is non-trivial in JavaScript since it is a dynamically typed language. That means there is no type signature, type annotation, or type anything to clue a person or a static analyzer into what types are expected.

It turns out LambdaTester's algorithm did generally well for most common methods, but failed for methods that:
* had complex type requirement pre-condition checks, ex: `foo(Number, Number, Number, Number, Callback)`
* had optional arguments, ex: `readFile(Number, [optional], Callback)`

This makes sense given the algorithm's basic design:
*For 100 iterations, randomly permute the set of argument input types (and values) with a callback as the final argument from position 1...5.*

So, as a curious and naive undergrad, I set out to see if I could do better.

![Let's do this](https://media.giphy.com/media/CjmvTCZf2U3p09Cn0h/giphy.gif)

***

## Putting the Brute in Brute Force

With a couple months of algorithm practice as part of interview preperation, I knew the first move was to check out a brute force solution. This is rather obvious and was quick to implement. Basically, for the first five argument positions (a hard coded assumption made by the paper) I generated all possible combinations of types with a callback at the end. Satisfyingly, this exhaustive approach was correct, finding every single callback position. However, as with any of these brute force algorithms, it did not scale well. For this particular input size, my code tried over 1500 combinations, a fairly non-trivial 15x more than LambdaTester's algorithm. Some quick math:
```
type = number | null | object | array | string | boolean

POS_1: cb (1 = 1)
POS_2: type, cb (6 * 1 = 6)
POS_3: type, type, cb (6 * 6 * 1 = 36)
POS_4: type, type, type, cb (6 * 6 * 6 * 1 = 216)
POS_5: type, type, type, type, cb (6 * 6 * 6 * 6 * 1 = 1296)

Total: 1555
```

Thus, we more generally have a (6^(n-1)) + (6^(n-2))... or roughly an exponential 6^n complexity, which is **really bad**, especially given the initial algorithm is constant time, or 100 iterations no matter the size of n arguments we want to discover. 

***

## How can we optimize this?

Using my problem solving strategies from [The Algoithm Design Manual](http://algorist.com/), I searched for ways to reduce repeated work or prune the search space. Ultimately turns out there is no good way to do so since:
1. every test is unique and so no work is redundant
2. any test can be the right answer (cannot infer a direction based on a previous outcome)
3. related to 2, there is no way to determine the partial correctness (output is binary)

Hmmm... time to phone the audience.

![me calling people](https://media.giphy.com/media/xUOrwiovjeOo1qQAY8/giphy.gif)
***
## Type Inference in the Wild

Avoiding the more intensly type theoretical papers, I ended up focusing my search on three, well-used, open source projects:
1. [TypeScript](https://www.typescriptlang.org/): superset of JavaScript that transcompiles to JavaScript, supporting futuristic ECMAScript features and some static typing
2. [Flow](https://flow.org/): lightweight, static type checker for JavaScript
3. [Google's Closure Compiler](https://developers.google.com/closure/compiler): JavaScript to JavaScript optimizing compiler

While all three more-or-less have some degree of type inference, the Closure Compiler is less focused on it, and Flow [seems to be on the decline](https://medium.com/flow-type/what-the-flow-team-has-been-up-to-54239c62004f):
> Recently a bunch of open-source projects originally created at Facebook published plans to be rewritten in TypeScript. At Facebook we strongly value the independence of individual teams in creating their roadmaps, and in doing the best they can for the products they build. The projects that have decided to switch to TypeScript have external contributors whose lives will be much easier with this switch, and we respect these decisions.

and furthermore, was less interesting because it seemed to be veering on the side of [*required annotations*](https://medium.com/flow-type/asking-for-required-annotations-64d4f9c1edf8) (something I naively believed to be due to a deficiency in innovation). So, I jumped headfirst into TypeScript's source code with the thinking *"if it can tell me what types are and are not correct, then it must have some idea around what types are correct and expected..."*

![later](https://media.giphy.com/media/l4FGJPnSGn9K5wcTK/giphy.gif)

**AND WE ARE BACK AT THE BEGINNING OF THIS POST...**

***

## Conclusion - Gradual Typing and this Thing Called Heuristics

This evening, before diving back into my partial solution from above, I decided to do a bit of searching into this concept I had seen around the internet called **Gradual Typing**. Settling upon [an article by Jeremy Siek](https://wphomes.soic.indiana.edu/jsiek/what-is-gradual-typing/), I learned that gradual typing is a type system where static and dynamic types coexist, often via type annotations...

**WAIT A MOMENT... isn't this kind of like TypeScript!**

Going back to Google, I did another search and [it appears that TypeScript is a soft sort of gradually typed language](https://itnext.io/typescript-static-or-dynamic-64bceb50b93e) (not going to pretend I 100% know what that means... just learned about gradual typing three hours ago). Ah, this makes sense. **Thus, it is expected that you cannot fully infer every single method type signature!!!**

Ok, so it seems this TypeScript, type annotation crutch is doomed to fail; furthermore not every JavaScript library has a type header file. Now what? Well, coincidentally, I got to the seventh chapter in the *Algorithm Design Manual*, a chapter discussing heuristics. The basic idea here is that we can use some stats and probability to get a mostly correct answer, sacrificing correctness for, typically, a significant improvement in efficiency. However, this comes with a catch. **We must be able to define a cost function to weigh our progress towards the optimal solution.** As discussed above, since ever input is unique and only correct or incorrect, **no such cost function exists.**

![sad face](http://giphygifs.s3.amazonaws.com/media/ISOckXUybVfQ4/giphy.gif)

Well, it turns out my work here is done. I may not have much to show for my adventure here, but I have thoroghly convinced myself that the current algorithm is very likely the best.
