---
layout: post
title:  "[WIP] Pyret Types Part 3: How a New Type Might be Implemented"
date:   2023-06-02 12:00:00 +0700
tech: true
---

This is the thurd post in a multi-part series where we go deep to understand how types function in the Pyret programming langauge with the goal of eventually introducing our own type for tables. In the previous post...

## Testing

While Pyret is not quite production ready, it is often a budding programmers first programming langauge. Thus, we _do_ need unit tests to ensure some level of confidence modifications to the core runtime are correct.

The obvious place to begin our exploration here is in `./tests` within the main Pyret repository.

### How Tests are Executed

### Existing Type Checker Tests


