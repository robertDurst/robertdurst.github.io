---
layout: post
title:  "Pyret Types Part 1: an Initial Exploration"
date:   2023-05-27 12:00:00 +0700
tech: true
---

This summer I am beginning research with a primary goal of adding a new type to the [Pyret Programming Langauage](https://pyret.org/). While I have hacked on my own programming langauge ([Sailfish](https://github.com/sailfish-lang)) and contributed to a [paper published as part of 2022 IEEE/ACM 44th International Conference on Software Engineering (ICSE)](https://software-lab.org/publications/icse2022_Nessie.pdf), I have yet to _really_ contribute to a programming language. 

## What is Pyret?

> Pyret is a programming language designed to serve as an outstanding choice for programming education while exploring the confluence of scripting and functional programming. It's under active design and development, and free to use or modify.
-- [pyret.org](https://pyret.org/)

### Getting Started

The easiest way to experiment with Pyret and see the type-checker in action:

1. open your browser of choice and navigate to the CPO ([code.pyret.org](https://code.pyret.org/))
2. click Open Editor
3. write some code with annotations in the left side (definition area)
```pyret
add-and-to-string :: Number, Number -> String
fun add-and-to-string(num-1, num-2):
    sum = num-1 + num-2
    to-string(sum)
end
```
4. click the drop down next to run and select `Type-check and run (beta)`
![`Type-check and run (beta)` button](../../assets/images/type_check_REPL_find.png)
5. click `Type-check and run (beta)`
6. in the interactive area (right side), type `add-and-to-string(1, 10)` and hit enter
7. observe `"11"` outputted in the console on the right
8. rewrite and introduce a bug
```pyret
add-and-to-string :: Number, String -> String
fun add-and-to-string(num-1, num-2):
    sum = num-1 + num-2
    to-string(sum)
end
```
9. click `Type-check and run (beta)` and observe the type check error
![`Type-check and run (beta)` button](../../assets/images/type_check_REPL_failure.png)


### Optional Type Checking

Ultimately, Pyret is a dynamic language with annotations and an optional type checker. Thus, consider the following levels of type enforcement for a method that takes in a number and calculates the square.

**Example 1: no annotations and type checker off**
```
fun square_num(n):
  n * n
end
```

**On execution with the wrong input type:**
```
> square_num("10")
Evaluating this Times (*) expression errored:
  n * n
The left side was:

"10"
The right side was:

"10"
The * operator expects to be given two Numbers.
```

**Example 2: input annotations and type checker off**
```
fun square_num(n :: Number):
  n * n
end
```

**On execution with the wrong input type:**
```
> square_num("10")
The Number annotation
    fun square_num(n :: Number):
was not satisfied by the value

"10"
```

**Example 3: input annotations and type checker on**
```
fun square_num(n :: Number):
  n * n
end
```

**On execution with the wrong input type:**
```
> square_num("10")
Type checking failed because of a type inconsistency.

The type constraint Number was incompatible with the type constraint String
```

**Example 4: full function annotations and type checker on**
```
square_num :: Number -> Number
fun square_num(n):
  n * n
end
```

**On execution with the wrong input type:**
```
> square_num("10")
Type checking failed because of a type inconsistency.

The type constraint Number was incompatible with the type constraint String
```

**Example 5: full function annotations and type checker on with incorrect function implementation**
```
square_num :: Number -> Number
fun square_num(n):
  n * n
end
```

**On compilation:**
```
Type checking failed because of a type inconsistency.

The type constraint Number was incompatible with the type constraint String
```

***

## Conclusion

Since type checking is off by default, unless users add type annotations, new types won't, alter the compilation or execution process of existing Pyret programs. However, we _do_ enable better tooling such as an improved language server (al beit [the only language server is a WIP](TODO: find link)) or even filling type holes in improved REPL editors like [Repartee](https://kilthub.cmu.edu/articles/conference_contribution/Combining_Interactive_and_Whole-Program_Editing_with_REPARTEE/19787683?backTo=/collections/PLATEAU_2022/5957631).

So, where do we begin? As pointed out by my [faculty advisor from the University of Utah Ben Greenman](https://ben-greenman.com/), a good starting point is to check out the typechecker to investigate the logic around enforcing types.

