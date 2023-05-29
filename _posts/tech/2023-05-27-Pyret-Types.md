---
layout: post
title:  "[DRAFT] Pyret Types: a Depp Dive"
date:   2023-05-27 12:00:00 +0700
tech: true
---

This summer I am beginning research with a primary goal of adding a new type to the [Pyret Programming Langauage](https://pyret.org/). While I have hacked on my own programming langauge ([Sailfish](https://github.com/sailfish-lang)), contributed to a [paper published as part of 2022 IEEE/ACM 44th International Conference on Software Engineering (ICSE)](https://software-lab.org/publications/icse2022_Nessie.pdf), and even [contributed to a rust WASM project, wasm-bindgen](https://github.com/rustwasm/wasm-bindgen/pulls?q=is%3Apr+author%3ArobertDurst+is%3Aclosed), I have yet to _really_ contribute to a programming language. 

Pyret is a dynamic language with an optional type checker. Thus, adding types don't, by default, alter the compilation or execution process of Pyret programs. However, we _do_ enable better tooling such as an improved language server (al beit [the only language server is a WIP](TODO: find link)) or even filling type holes in improved REPL editors like [Repartee](https://kilthub.cmu.edu/articles/conference_contribution/Combining_Interactive_and_Whole-Program_Editing_with_REPARTEE/19787683?backTo=/collections/PLATEAU_2022/5957631).

So, where do we begin? As pointed out by my faculty advisor (** TODO: ask if I can name him/her here **), a good starting point is to check out the typechecker to investigate the logic around enforcing types.

## Getting Started

The easiest way to see the type-checker in action:

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

## The Typechecker

[Typechecker code block from the runtime starts here](https://github.com/brownplt/pyret-lang/blob/horizon/src/js/base/runtime.js#L1307).
[tyopeMisMatch error is defined in the ffi file here](https://github.com/brownplt/pyret-lang/blob/horizon/src/js/trove/ffi.js#L286).
Note how this is thrown in the main type check logic from the runtime:
```js
function checkType(val, test, typeName) {
    if(!test(val)) {
        thisRuntime.ffi.throwTypeMismatch(val, typeName);
    }
    return true;
}
```

Ultimately `checkType` is called from the `makeCheckTypeMethod` [in the same runtime file](https://github.com/brownplt/pyret-lang/blob/horizon/src/js/base/runtime.js#L1354):

```js 
var makeCheckType = function(test, typeName) {
    if (arguments.length !== 2) {
        // can't use checkArity yet because thisRuntime.ffi isn't initialized
        throw("MakeCheckType was called with the wrong number of arguments: expected 2, got " + arguments.length);
    }
    return function(val) {
        if (arguments.length !== 1) { var $a=new Array(arguments.length); for (var $i=0;$i<arguments.length;$i++) { $a[$i]=arguments[$i]; } throw thisRuntime.ffi.throwArityErrorC(["runtime"], 1, $a, false); }
        return checkType(val, test, typeName);
    };
}
```

Let's examine a few things:
1. the inputs
2. the almost minified js returned

**inputs**:
* _test_: the js method which actually tests if the concrete value is the type. Consider the following examples:
    * `jsnums.isRational`: a modified version of [Danny Yoo's js-numbers library](https://github.com/dyoo/js-numbers) which ships with Pyret [here](https://github.com/brownplt/pyret-lang/blob/horizon/src/js/base/js-numbers.js)
    * `isTuple`
        ```js
        function isTuple(val) {
        return val instanceof PTuple;
        }
        ```
* _typeName_: a string value representing the name of the type, useful when debugging and presenting errors back to the user

**the returned value**: ultimately, we return a function here. Unminified, expanded, and annotated, the returned method source looks like this:


```js
function(val) {
    // arity check
    if (arguments.length !== 1) { 
        // create a new array the size of arguments
        var $a = new Array(arguments.length); 
        // put the arguments in our argument array
        for (var $i = 0; $i < arguments.length; $i++) { 
            $a[$i] = arguments[$i]; 
        }

        // return an FFI arity mismatch error, i.e. we expected an arity of 1, but received an arity > 1
        // NOTE(rob): I think explicitly calling throw here is redundant?
        throw thisRuntime.ffi.throwArityErrorC(["runtime"], 1, $a, false); 
    }

    // if arity == 1, then we execute check type with the following inputs:
    //  _val_: the actual concrete value to test
    //  _test_: the logic to check if the type is correct
    //  _typeName_: a string of the type name, i.e. "String", "Boolean", "Number", etc.
    // return checkType(val, test, typeName);

    // if the test fails, we throw
    // NOTE(rob): it seems the type check logic is a bit inconsistent in that it has two approaches to handling failure:
    //  1. return true/false (in the case of success)
    //  2. throw/don't throw (in the case of failure)
    // Seems like maybe a code smell? Will have to dig deeper to see have the type checks are used
    if(!test(val)) {
        thisRuntime.ffi.throwTypeMismatch(val, typeName);
    }

    return true;
}
```

## Pyret's Type System


### Types checked

Since `makeCheckType` is a method generator for the `checkType` method, we can then look for the instances where `makeCheckType` is called to figure out the scope of type checking in Pyret.

### Some Context - Brands

> Brands are a mostly internal language concept, useful for implementing custom datatypes.

[Pyret documentation on brands can be found here](https://pyret.org/docs/latest/brands.html).

A concrete example of this is the `table` type. For the table type, we type check with the following method:

```js
var get = runtime.getField;
// creates a new brand
var brandTable = runtime.namedBrander("table", ["table: table brander"]);

function isTable(val) {
    return hasBrand(brandTable,  val);
}

function hasBrand(brand, val) {
    // this is a way of calling the method test with the argument being `val`, comparable to the .send method in Ruby
    return get(brand, "test").app(val);
}
```


### Comprehensive list of Types

| Type Name         | Type Check                                                    | Where Defined                  | Notes                                |
| ----------------- | ------------------------------------------------------------- | ------------------------------ | ------------------------------------ |
| List              |                                                               | src/js/base/post-load-hooks.js |                                      |
| EqualityResult    |                                                               | src/js/base/post-load-hooks.js |                                      |
| Table             | in src/js/trove/table.js checks if the value "has this brand" | src/js/base/post-load-hooks.js | implemented as a brand               |
| Row               | in src/js/trove/table.js checks if the value "has this brand" | src/js/base/post-load-hooks.js | implemented as a brand               |
| CellContent       |                                                               | src/js/base/post-load-hooks.js |                                      |
| String            |                                                               | src/js/base/runtime.js         |                                      |
| Number            |                                                               | src/js/base/runtime.js         |                                      |
| Exactnum          |                                                               | src/js/base/runtime.js         |                                      |
| Roughnum          |                                                               | src/js/base/runtime.js         |                                      |
| NumInteger        |                                                               | src/js/base/runtime.js         |                                      |
| NumRational       |                                                               | src/js/base/runtime.js         |                                      |
| NumPositive       |                                                               | src/js/base/runtime.js         |                                      |
| NumNegative       |                                                               | src/js/base/runtime.js         |                                      |
| NumNonPositive    |                                                               | src/js/base/runtime.js         |                                      |
| NumNonNegative    |                                                               | src/js/base/runtime.js         |                                      |
| Array             |                                                               | src/js/base/runtime.js         | commented out                        |
| Tuple             |                                                               | src/js/base/runtime.js         |                                      |
| RawArray          |                                                               | src/js/base/runtime.js         |                                      |
| Boolean           |                                                               | src/js/base/runtime.js         |                                      |
| Object            |                                                               | src/js/base/runtime.js         |                                      |
| Function          |                                                               | src/js/base/runtime.js         |                                      |
| Method            |                                                               | src/js/base/runtime.js         |                                      |
| Opaque            |                                                               | src/js/base/runtime.js         |                                      |
| Pyret Value       |                                                               | src/js/base/runtime.js         |                                      |
| Natural Number    |                                                               | src/js/base/runtime.js         |                                      |
| Srcloc            |                                                               | src/js/trove/ffi.js            |                                      |
| ErrorDisplay      |                                                               | src/js/trove/ffi.js            |                                      |
| Graph             |                                                               | src/js/trove/graph.js          |                                      |
| Tree              |                                                               | src/js/trove/graph.js          |                                      |
| CoreInfo          |                                                               | src/js/trove/particle.js       |                                      |
| core              |                                                               | src/js/trove/particle.js       |                                      |
| PinConfig         |                                                               | src/js/trove/particle.js       |                                      |
| Event             |                                                               | src/js/trove/particle.js       |                                      |
| CoreInfo          |                                                               | src/js/trove/particle.js       |                                      |
| ConfigInfo        |                                                               | src/js/trove/particle.js       |                                      |
| MutableStringDict |                                                               | src/js/trove/str-dict.js       |                                      |
| StringDict        |                                                               | src/js/trove/str-dict.js       |                                      |
| MutableStringDict |                                                               | src/js/trove/string-dict.js    | how is this different from str-dict? |
| StringDict        |                                                               | src/js/trove/string-dict.js    | how is this different from str-dict? |

### How the type checker is used

Let's start with an example of `checkBoolean`. It's definition is:

```js
var checkBoolean = makeCheckType(isBoolean, "Boolean");

function isBoolean(b) {
    return b === !!b;
}
```

It is used in just a few places within the codebase to enforce types within internally defined methods. As an example:

```js
function throwArityError(funLoc, arity, args, isMethod) {
    checkSrcloc(funLoc);
    runtime.checkNumber(arity);
    runtime.checkList(args);
    runtime.checkBoolean(isMethod);
    raise(err("arity-mismatch")(funLoc, arity, args, isMethod));
}
```

This runs a series of type checks before throwing the arity error. Specifically, `checkBoolean` checks if `isMethod` is a bool.

So that is a specific instance. The question I am most interested in is, when we execute the `Type-check and run (beta)` from above, when is the type checking applied? When do we check if something is a boolean? For this mission, we may have to navigate to [the source of COP](https://github.com/brownplt/code.pyret.org).

[For starters, here is where the repl ui actually runs the code](https://github.com/brownplt/code.pyret.org/blob/horizon/src/web/js/repl-ui.js#L828).















