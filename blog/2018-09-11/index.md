---
date: "2018-09-11"
title: "Functional Javascript: Pure Function"
category: "Javascript"
---

This is the 2nd part of my functional Javascript series. If you missed out on the first part, don't worry, this does not require any knowledge from it.

## Introduction

Let's start with a definition of a **pure function** from Googling:
> A pure function is a function where the return value is only
> determined by its input values, without observable side effects.

In Javascript, i always get confused between the functions `splice` and `slice`, given
how similar they are named. They also perform the same thing but by doing it differently!

```javascript
const letters = ['a', 'b', 'c', 'd', 'e']

letters.slice(0, 2) // ['a', 'b']
letters.slice(0, 2) // ['a', 'b']
letters.slice(0, 2) // ['a', 'b']

letters.splice(0, 2) // ['a', 'b']
letters.splice(0, 2) // ['c', 'd']
letters.splice(0, 2) // ['e']
```

From quick observation, `slice` gives the same results no matter how many times we called it, where as
splice gives a different result until the array is exhausted.

In fp, `slice` is what we call **pure function** and does not *mutate* data. On the other hand,
`splice` is **impure**, it mutates data every time we call it. It makes code less predictable
since it does not return the same result on every subsequent call.
We should always strive for pure functions to help eliminate unexpected behaviours in our code.

## What makes a function pure?

```javascript

//impure
let name = 'Graham'
const sayHello = () => { return `Hello $name` }

sayHello() // Hello Graham

name = 'David'
sayHello() //Hello David

//pure
const sayHello = () => {
    let name = 'Graham'
    return `Hello $name`
}
```
In the impure version, the function results are impacted by external variables. This can make our code hard to reason about. This is a trivial example but we can imagine these can become extremely hard to follow and debug in a huge code base.

On the other hand, the pure version is independent and does not rely on any external variables. 
Reading the function, we should expect no surprises on the expected results. Basically, to be pure,
a function should have no **side effects**.

## What are side effects?

A list of possible side effects: 
- reading input from keyboard
- writing output to a file
- making an api call
- changing external variables
- reading from database

Side effects are usually the cause of unexpected behavours or bugs in systems, but if we don't have side effects, how can we write meaningful software? The answer to that is that we should not get rid of it but contain them. We will learn how to do that by using *functors* and *monads* in later chapters when i get around to it.

## Caching/Memoization

From wikipedia, memoization is defined as: 
> Memoization is an optimization technique used primarily to speed up computer programs by storing the results of 
> expensive function calls and returning the cached result when the same inputs occur again.

If the function is pure, then it always returns the same results. Then why do we have to recompute
the same calculations over and over again? So we can just cache it!
This can help speed up processing time, especially in a high computing environment.

Here is a trivial example of using memoization where we calculate the length of a string:
```javascript
const calculateLength = memoize(string => string.length)

calculateLength('bear') // 4

calculateLength('bear') // should return 4 from cache
```
Libraries such as *lodash* or *ramda* have their own memoize function built-in.

A simple implementation of memoize would be something like this:
```javascript
const memoize = func => {
    const cache = {};

    return arg => { //only support single argument
        cache[arg] = cache[arg] ||  func(arg);
        return cache[arg];
    }
}
```

## Conclusion

So from our understanding, pure function is essentially
- a function that has no side effects
- returns the same results every time it was called

Writing pure functions not only helps decrease our *cognitive load*, but we can also get performance improvements by using memoization. In a world where parallel computing is everywhere, being pure also eliminates race conditions, imagine the time saved from debugging on race conditions in multi threaded environments. This is definitely a win for us developers!