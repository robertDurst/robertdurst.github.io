---
layout: post
title:  "[Category Theory Series] I: To Categories From First Principles"
date:   2020-04-03 19:19:21 +0700
---

Welcome! Somehow you have managed to find the first post in my *Category Theory Series*. Embarrassingly, my motivation for diving into Category Theory partially comes from a desire to understand the humor [in this video](https://www.youtube.com/watch?v=ADqLBc1vFwI). Yet from the most innocent of beginnings, here I am, here you are. What's this nonsense all about?

<div style="text-align:center"><img src="https://media.giphy.com/media/SSirUu2TrV65ymCi4J/giphy.gif" /></div>

## Some People Smarter than Me Say Things About Category Theory

So why is this stuff interesting? Since I have no clout here, I am just YAPB (yet-another-programming-blog) I will [phone a friend](https://www.youtube.com/watch?v=SpgVlTJibc0), or well, just look for Category Theory quotes from really smart people.

*"Category theory is both an interesting object of philosophical study, and a potentially powerful formal tool for philosophical investigations of concepts such as space, system, and even truth."* - [Stanford Encyclopedia of Philosophy](https://plato.stanford.edu/entries/category-theory/)

*"Category theory can be helpful in understanding Haskell's type system."* - [Haskell Wiki](https://wiki.haskell.org/Category_theory)

*"In my opinion, category theory isn't so much another country-on-the-map as it is a means of getting a bird's-eye-view of the entire landscape. It's what lifts our feet off the grass and provides us with a sweeping vista from the sky."* [Tai-Danae Bradley PhD](https://www.math3ma.com/blog/what-is-category-theory-anyway
)

*"Category theory is becoming a central hub for all of pure mathematics.... We believe that it has the potential to be a major cohesive force in the world, building rigorous bridges between disparate worlds, both theoretical and practical."* - [Seven Sketches in Compositionality: An Invitation to Applied Category Theory](http://math.mit.edu/~dspivak/teaching/sp18/7Sketches.pdf)

***

# To Categories From First Principles

### Preface
**STOP:** do not read any further. Before beginning your journey here, you must first *unthink* and *unknow* any presumptions you may have about Category Theory, Sets, Math, the World, etc. Let's begin with an absolutely clean slate.

***

### Object

We will start with an `object`. An object is simply a thing, yep, literally anything. This thing has no meaning. Here are some things:
* cat: 🐈
* face: 😀
* one: `1`
* pizza: 🍕

In your everyday life, these objects may have meaning. You may be able to also do things with them, like *eat a 🍕* or *feed a 🐈*. Unfortunately, that is not true in this world, at this time. These are objects, nothing more - do you get the point yet?

***

### Arrow

It may be desirable to transition, or *travel*, from one object to another. In this world, that is possible via one way roads, called an `arrow`. We don't know anything about these *one way roads*. In real life, they could be bumpy, pristine, fast, slow, curvy, straight, etc. Here all we know is that the road goes from some object `A` to some object `B`. Consider the following arrows (➡️):
* `f`: 🐈 ➡️ 🍕
* `g`: `1` ➡️ `2`
* `h`: 😀 ➡️ 😀

Notice how the last arrow goes from face to face. Don't be confused. If you are confused, please reread the preface. All we can assume about an arrow is that it goes from `A` to `B`. Also notice that these arrows are `directed` meaning `1` ➡️ `2` goes from `1` to `2`, but not `2` to `1`.

***

### Set

Sometimes it's pretty cool to have more than one of something (unless that thing is parking tickets). This collection of things exists in our world! We call it a `set`. Here are a few examples:
* **A set of objects:** `O` = { 🍊, 🍋, 🍌 }
* **A set of arrows:** `A` = { `f`, `g`, `h` }
* **The empty set:** ∅ = {}

***

### Graph

By now, you're probably wondering, *"what's the point of any of this if I can't do anything with it?"* Well look no further. Consider a world called `graph` made up of a set `O` of objects and a set `A` of arrows between these objects (some may call these `edges` and `vertices`). <br>
<br>
A graph `G` with: `O` = { 🍊, 🍋, 🍌 } and `A` = { `f`, `g`, `h` } may look like this:<br>
<div style="text-align:center"><img src="https://imgur.com/Jdd6gxd.png" /></div>
<br>
Let's introduce some `operations`:
* **domain (dom)**: given an `arrow` return the `object` to its left
* **codomain (cod)**: given an `arrow` return the `object` to its left

<br>
**Example:**<br>
<div style="text-align:center"><img src="https://imgur.com/zdvYxir.png" /></div>

While addition of these trivial operations may seem pointless, we are now able to *talk* more about our world: *"this object is the domain of that arrow"*. Before, while you and I could *see* that two objects are related by an arrow, we had no way of formally describing which object was on which side of the directed arrow. These operations will be important as we continue to build up our world.

***

### Category

Ah yes, things are getting real! Let's talk about categories. To begin, we are building off of a graph - thus we have a set `O` of objects, a set `A` of arrows between objects and the `domain` and `codomain` operations. We begin by adding a couple more `operations`:
* **identity**: an arrow `IDa` from some object `A` back to itself.  <br>
<div style="text-align:center"><img src="https://imgur.com/jwQs7gw.png" /></div>
* **composition:** for a pair of arrows `f` and `g`, denoted <`f`, `g`>, where `cod(f)` = `dom(g)`, we have an arrow `g ; f` called the *composite*, where we read `g ; f` as first apply `f` then apply `g`.<br>
<div style="text-align:center"><img src="https://imgur.com/8HHNq6P.png" /></div>

**STOP:** before moving forward, let's ponder on the meaning of an operation. What is an operation? If you're a math person, think about functions. What is a function? Let's get a little philosophical and step back for a moment. An operation takes something as input and returns something else as output. `cod` takes an arrow and returns the `object` to its right. `composition` takes a pair of arrows and returns their `composite`. From one thing, we get, or observe another. At its heart, an operation is an **observation** or a way of deriving information from some source. They can be used together, like legos, allowing you to form more complex observations from smaller ones. Yet, observations themselves are not necessarily useful - just because something happened does not give us the basis for any sort of reasoning, **we cannot make any assumptions**.<br>
<br>
To make assumptions, we need rules, established truths, **axioms**. In the world of categories, we establish our first axioms! We'll start with `unit law` <br>
<br>
**Unit law:** for every arrow  `f`: 😀 ➡️ 🍕 and every  `g`: 🍕 ➡️ 🐈, composition with `ID🍕` gives: <br>
`ID🍕 ;  f` = `f` and `g ; ID🍕` = `g`<br>
From this we get the following commutative diagram:
<div style="text-align:center"><img src="https://imgur.com/maKCj5n.png" /></div> <br>
<br>
**Associativity:** in this world, objects are a friendly (~polygamous~?) bunch , not too concerned who they *associate* with. Thus, for some category with objects and arrows: `f`: 🐈 ➡️ 🍕, `g`: 🍕 ➡️ 😀, and `h`: 😀 ➡️ 🍌 we have:<br>
`h ; (g ; f)` = `(h ; g) ; f`<br>
From this we get the following commutative diagram:
<div style="text-align:center"><img src="https://imgur.com/ZGVvdHo.png" /></div>

These axioms, or assumptions, are **very important**. They explain **truths** about observations - i.e. you observe that some object `a` has `IDa`. Therefore, this `IDa`, when composed with `f` and `g` such that `cod(f) = a` and `dom(g) = a`, we know that these compositions, properly ordered, will result in `f` and `g` respectively. As another example, let's say you observe `cod(f) = dom(g)` and then further observe that `< f, g >` may be composed to form the composite `g ; f`. Given some other arrow `h` where `dom(h) = cod(g)`, we then know that we can place the parenthesis anywhere due to associativity.<br>
<br>

***

### Commutative Diagrams

The significance of commutative diagrams is that, for any two objects `A` and `B`, every path (of arrows) between these objects, when composed, results in equal arrows. Thus, in the diagram above (for associativity), we have `h ; (g ; f) = (h ; g) ; f = h ; g ; f`. Let's take a walk...<br>
1. `h ; (g ; f)`
  * **Start:** 🐈
  * Take `g ; f`: 😀
  * Take `h`: 🍌
  * **END:** 🍌
2. `(h ; g) ; f`
  * **Start:** 🐈
  * Take `f`: 🍕
  * Take `h ; g`: 🍌
  * **END:** 🍌
3. `h ; g ; f`
  * **Start:** 🐈
  * Take `f`: 🍕
  * Take `g`: 😀
  * Take `h`: 🍌
  * **END:** 🍌

From the above, it's clear `1 = 2 = 3`.

***

### So what?
Well, we now have the ability to **combine** and discuss **equality** of arrows. With these tools, we can begin to reason about transitions between objects within our world in interesting ways. Without the addition of any more operations (observations) or axioms (assumptions), we can draw parallels to the real world to make some sense of this: <br>

**Driving** (*get from one location to another*): <br>
*[objects]:* locations<br>
*[arrows:]* roads<br>

**Surfing the internet** (*get from one webpage to another*):<br>
*[objects:]* webpages<br>
*[arrows:]* links<br>

**Baking** (*combine ingredients to make tasty treats*):<br>
*[objects:]* ingredients<br>
*[arrows:]* directions like mix, chop, put in oven, etc.<br>

**Coding** (*get from one type to another*):<br>
*[objects:]* types<br>
*[arrows:]* functions<br>

With four operations and two axioms AND ONLY these four operations and two axioms, we can already reason about transitions and relations between objects within very different worlds (or structures).

<div style="text-align:center"><img src="https://media.giphy.com/media/1034EEGrn91SrS/giphy.gif" /></div>
