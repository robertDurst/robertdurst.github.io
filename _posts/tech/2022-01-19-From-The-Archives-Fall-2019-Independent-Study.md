---
layout: post  
title:  "From the Archives: Independent Study 2019 Summary"
date:   2022-01-18 19:19:21 +0700   
tech: true
---

Was digging through old repos on GitHub and came across my write-up for my Fall 2019 Independent Study at Colby College. This is the work which will result in me being mentioned in *Nessie: Automatically Testing JavaScript APIs with Asynchronous Callbacks* which is being presented at [the ICSE 2022 conference](https://conf.researchr.org/details/icse-2022/icse-2022-papers/69/Nessie-Automatically-Testing-JavaScript-APIs-with-Asynchronous-Callbacks).

# Independent Study Fall 2019

In a thematic continuation of my Spring 2019 indepedent study, where I designed and implemented the [Sailfish](https://github.com/sailfish-lang/sailfishc) programming language, this Fall I undertook a research project, exploring the extension of a feedback-directed random test generation framework, called [LambdaTester](http://software-lab.org/publications/oopsla2018_LambdaTester.pdf), such that it could work with asynchronous code. My personal goal for this research was two fold:

1.  explore an interest in a research-based career path
2. dive into JavaScript internals... stop saying "JavaScript is whack" and actually understand why it is the way it is

To complete this research, I collaborated with [Professor Frank Tip](http://www.franktip.org/) of the [Northeastern University Programming Language Lab](https://prl.ccs.neu.edu/) and was advised by [Professor Bruce Maxwell](http://www.cs.colby.edu/maxwell/) of [Colby College](http://cs.colby.edu/).

## What is LambdaTester?

LambdaTester is a test generation framework that employs four different strategies for generating callbacks to higher order methods in JavaScript. It is comprised of a two part algorithm. The first stage is callback discovery. Callback discovery looks to "learn" which methods expect callbacks as input and at which position(s). It does this via a 100 trial and error algorithm, which attempts different combinations of random values and a simple callback that logs a message. If this method is executed and logs the message, the callback's position is recorded. The second stage is the actual test generation phase. This takes a few different inputs, such as the result from the first stage, an output directory, and whether or not to utilize a feedback-directed approach. The crux of this stage is the callback generation phase. There are four types of callbacks generated:

1. **empty:** a baseline test
2. **quick test:** using a haskell influenced library, generate random values to return
3. **callback mining:** follows the basic style/structure of callbacks from a corpus of code
4. **feedback-directed:** the most interesting and effective method, uses previously generated and derived values from outer scopes and previous tests

This framework was well received at OOPSLA 2018 and achieved impressive results, demonstrating incompatibility between versions of many well used, open-source, NPM packages. To learn more, checkout the paper linked above.

## AsyncLambdaTester Basics

The extension I explored focused on asynchronous callbacks. What is the difference between an asynchronous callback is when the callback gets executed -- either right away, or at some unspecified time in the future. Asynchronous code introduces complexity into code, breaking any sort of assumptions that can be placed on the order of execution of given statements. LambdaTester did not seek to introduce or generate asynchronous callbacks. Thus, an entire interesting area of possible errors was ignored. With asynchonous callback generation, ordering bugs and a whole new domain of faulty assumptions can really be tested. In order to implement this extension, I needed to implement two things:

1. classification of expected callbacks as either asynchronous or synchronous
2. a strategy for nesting method callbacks and reusing previously calculated/derived values

Unfortunately, due to extenuating circumstances (mostly job interview and prep) I was only able to finish the first step -- I came up with an algorithm and partially implemented 2, but do not have enough concrete to present (or want to expose the secret sauce for in-progress research).  

## A Failed Attempt at Improving Callback Discovery Prediction

Before I even started the process of implementing an async/sync classification algorithm, I decided to step back and see if I could improve the current callback discovery algorithm. The problem with the current algorithm is that it was not exhaustive, thus for methods that expected something like: number, number, number, callback, the callback was not being discovered. Thus I underwent an interesting, albeit unsuccessful delve down the type inference rabbit hole. You can read all about it here: [Adventures in PL: Inferring Function Signatures in JavaScript](https://robdurst.com/JavaScriptTypeInference).

## Classifying Callback parameters

The most clever and novel part of my work this Fall was the design and implementation of a method for classifying callbacks as either asynchronous or synchronous. The intuition came for this during a deep dive into how the Node.js event loop works. Here is a good video for the less familiar: [https://www.youtube.com/watch?v=8aGhZQkoFbQ](https://www.youtube.com/watch?v=8aGhZQkoFbQ)

As an example, consider the following code:
```javascript
const foo = () => console.log("A");
setTimeout(foo, 0);
console.log("B");
```

A totally sensible guess into what the output may be is "A" then "B"... however, that is not the case. But how can that be??? The timeout is set to 0ms... Well, it turns out that the timeout fires and then throws its callback method onto the event loop. Then, the rest of the code in the call stack executes, so "B" is logged. Once the call stack is empty, the asynchronous method, foo, is called, and "A" is logged. Crazy right? Well, not so much once you get the hang of the event loop.

We can use this to our advantage. Consider a setTimeout method that instead accepted a method that would be executed synchronously. Then we would get "A" and then "B" as expected. Thus, if we execute methods as follows:
```javascript
const foo = () => console.log("A");
someMethod(foo);
console.log("B");
```

We can classify the callback as asynchronous or synchronous depending on the ordering of the logged messages. It turns out this worked wonderfully, as tested against modules within the Node.js standard libraries.

## One Caveat

However there were some cases where my method above failed... some really interesting edge cases. It turns out sometimes callbacks may be classified as both asynchronous and synchronous. So how can a method be synchronous and asynchronous? When working with various methods in the *zlib* module, AsyncLambdaTester classified methods as both asynchronous and synchronous:
```bash
Discovery phase...  
[{  
	name: 'deflate',  
	positions: [ '2: sync', '2: async', '3: sync', '3: async' ]  
}]
```

Let’s dive into some source code!
```javascript
// Convenience methods.  
// compress/decompress a string or buffer in one step.  
deflate: createConvenienceMethod(Deflate, false)
```

Hmmm… so it turns out deflate is a sort of convenience method around a more general Deflate function that works on a Deflate object. Let’s jump to the convenience creator:

```javascript
function  createConvenienceMethod(ctor, sync) {
	// skipping to the interesting code...  
	return  function  asyncBufferWrapper(buffer, opts, callback) {  
		if (typeof opts === 'function') { callback = opts; opts = {}; }  
			return zlibBuffer(new ctor(opts), buffer, callback);  
		};  
	}  
}
```

Here we can see that an asynchronous method is created. If options are given, then the callback is the third method, otherwise it is the second. Let’s jump to the zlibBuffer method and see what’s going on there:

 ```javascript
function  zlibBuffer(engine, buffer, callback) {  
	// skipping to the interesting code...  
	engine.cb = callback;  
	engine.on('data', zlibBufferOnData);  
	engine.on('error', zlibBufferOnError);  
	engine.on('end', zlibBufferOnEnd);  
	engine.end(buffer);  
}
```


Ah! So here we see that the callback is invoked in three different scenarios by an engine object (the zlib engine, [a project dependency](https://github.com/nodejs/node/tree/8b4af64f50c5e41ce0155716f294c24ccdecad03/deps/zlib)). Thus, the callback is not immediately pushed to the event loop to be invoked asynchronously, but actually may be called earlier. Let’s experiment! Consider the following test code:

```javascript
const zlib = require('zlib');  

const f = (i) => {  
	return  () => console.log(`cb: #${i}`);  
}  

zlib.deflate("", f(2));  
zlib.deflate(1, f(1));  
console.log("A");
```

When we execute the above, we get:

```bash
cb: #1  
A  
cb: #2
```

Ah ha! We have found the source of our confusion. Based on the realization of the zlib engine above, we see that if we give deflate the wrong type for the first parameter, the callback executes immediately -- it is not pushed onto the event loop to be executed asynchronously. However, if deflate gets the correct type, even if the value is nonsensical/incorrect, it will execute asynchronously as expected.

## Some Implementation Details - To Typescript We Go

An unanticipated sub goal that I accomplished during this study was gaining some familiarity with TypeScript. A rather lengthy and largely unnecessary, yet learning filled part of this study was cleaning up the LambdaTester codebase and writing all my extensions in TypeScript. This required, learning TypeScript, using linters to enforce code style,  and restructuring the code into OOD-esque classes (much easier done in TypeScript than Node.js). Ultimately this turned out to be a wonderful use of my time as I handed off the project to another student at NEU. Instead of a jumble of attempts to generate asynchronous tests, I left a well commented, clean, organized codebase. As they say: "*Always code as if the person who ends up maintaining your code is a violent psychopath who knows where you live.*"

# Conclusion

Ultimately my independent study was a success, although maybe not quite in the way I originally hoped. For one, I certainly learned much more about how Node.js works internally. However, I did not finish my implementation of AsyncLambdaTester and also decided not to follow the research career path. That being said, I owe Professor Tip and Professor Maxwell for allowing me to pursue this project! Even though it did not quite work out as hoped, I am glad I will be able to look back and say "at least I tried it" and not have doubts about my journey directly into industry!
