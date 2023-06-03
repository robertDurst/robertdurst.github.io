---
layout: post
title:  "[WIP] Pyret Types Part 2: Types Under the Hood"
date:   2023-06-02 12:00:00 +0700
tech: true
---

This is the second post in a multi-part series where we go deep to understand how types function in the Pyret programming langauge with the goal of eventually introducing our own type for tables. In the previous post we set the stage for this work, [looking at how we can use the COP to explore the current state of type checking in Pyret](./2023-05-27-Pyret-Types-Part-1.md). In this post we'll look at how type checking functions under the hood.

## The Typechecker

Venturing down the stack, we find the `checkType` method. A fairly straightforward method, it either fails the predicate (`test`) and throws a type mismatch error, or it returns true ([jump to source](https://github.com/brownplt/pyret-lang/blob/horizon/src/js/base/runtime.js#L1307)).

```js
function checkType(val, test, typeName) {
    if(!test(val)) {
        thisRuntime.ffi.throwTypeMismatch(val, typeName);
    }
    return true;
}
```

Note that the type mismatch here is actually a method with some logic ([jump to source](https://github.com/brownplt/pyret-lang/blob/horizon/src/js/trove/ffi.js#L286)):

```js
function throwTypeMismatch(val, typeName) {
    // NOTE(joe): can't use checkPyretVal here, because it will re-enter
    // this function and blow up...
    if(!runtime.isPyretVal(val)) {
        console.log("Non Pyret value:", val);
        val = "non-Pyret value; see the console for more details";
    }
    runtime.checkString(typeName);
    raise(err("generic-type-mismatch")(val, typeName));
}
```

It is enough to understand "it just throws a type mismatch" for a reasonable mental model here.




Ultimately `checkType` is not called directly. Instead, it is used as a higher order function in the generator method `makeCheckType` which creates an instance of a method to check a specific type. Notice in the method definition for `throwTypeMismatch` we use `runtime.checkString` to make sure the typeName for the type mismatch is a String. Within the runtime we define `checkString` as:

```js
var checkString = makeCheckType(isString, "String");
```

and our generator `makeCheckType` is defined as ([jump to source](https://github.com/brownplt/pyret-lang/blob/horizon/src/js/base/runtime.js#L1354)):

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
    if(!test(val)) {
        thisRuntime.ffi.throwTypeMismatch(val, typeName);
    }

    return true;
}
```

## Pyret's Type System

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


### Types Checked

Since `makeCheckType` is a method generator for the `checkType` method, we can then look for the instances where `makeCheckType` is called to figure out the scope of type checking in Pyret.

















