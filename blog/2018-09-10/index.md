---
date: "2018-09-10"
title: "Functional Javascript: First Class Function"
category: "Javascript"
---

To kick off my new blog, i have decided to write about using functional programming concepts in Javascript. The reason i picked JS is because it is one of the most popular language in the world, so most people should be familiar with it and can just focus on learning the concepts.

## Introduction

What do we mean when we say functions are **first class**? It means we treat functions normally as how we treat any other data types. We can pass them around like variables, into a method and assign them to
variables.

Let's have a look at a simple line of code:

```javascript
const hello = name => `Hello $name`
```

Here we assigned a function into a variable called `hello` and being a variable, we can pass it around.

## Why bother?

By treating functions as first class, we can also condense the way we write our code:

```javascript
const goodbye = name => `Good bye $name` 
const cya = name => goodbye(name)

// above 2 lines are the same as
const goodbye = cya;
```
Here `cya` is just a wrapper on the function `goodbye`, they both take the same argument `name`,
so we can remove the extra code and just assign `cya` directly to `goodbye`.

Well, you might think why would anyone do this in real world, but here is a example of a repository class that we might bump into:

```javascript
const bookRepository = {
    addBook: book => Db.add(book),
    deleteBook: book => Db.delete(book),
    updateBook: (book, bookName) => Db.update(book, bookName),
    getBook: book => Db.get(book)
}

//which is the same as
const bookRepository = {
    addBook: Db.add,
    deleteBook: Db.delete,
    updateBook: Db.update,
    getBook: Db.get
}
```

Here we cleaned up the code, and now it is more readable. 

Before we finish, let's look at one last complicated example to get a more solid understanding of this concept.

```javascript
const getBook = callback => {
    return callDbToGetBook(book => {
        return callback(book)
    })
}

// is the same as 
const getBook = callDbToGetBook
```

Did you get it? Here is a hint:

```javascript
return callDbToGetBook(book => {
    return callback(book)
})

// is same as
return callDbToGetBook(callback)   
```

From this example, the first `getBook` function seems more complicated than it is. By eliminating the extra lines, we revealed the true intent of this function. This really shows how powerful first class function is.