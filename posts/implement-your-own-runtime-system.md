---
title: Implement your own runtime system
description: A brief introduction on how to get started implementing a runtime system fron scratch.
date: 2020-03-28
tags:
  - JavaScript
layout: layouts/post.njk
---

This is a brief introduction on **How to even get started?** implementing a runtime system.
If you’d like to get to the code directly, go check this repository out: https://github.com/pptang/goodtime/.

#### Disclaimer

This article won’t be a comprehensive and exhaustive guide to implement a runtime system and it may miss some crucial parts. After all, this project is just for fun and understanding the overall picture of a runtime system 😁.

## What is so-called runtime?

> A Runtime system, simply put, provides an environment where your programs can run.

For example, if you wrote a JavaScript program and want to run it, you probably will do as follow in your terminal:

```bash
> node main.js
```

It means you’ll run your `main.js` program inside Node.js runtime system.
That is, Node.js is a JavaScript runtime which can

- Understand JavaScript syntax
- Execute your JavaScript program
- Provide native APIs for your JavaScript program to do things like File I/O
- Manage the memory consumed by your JavaScript program

## Our Goal

> We would like to create a tiny, fork-able and understandable language runtime.

### By tiny

It means it’s easy to implement and to achieve this, we limit the language features to the bare minimum to be run on our runtime.

1. **Primitive types**: number, string, bool.
2. **Compound types**: array with `.map` and `.reduce`.
3. **Expressions**: case/switch, function call, operators.
4. **No statements inside the Function, except for variable declaration.**
5. **Lazy by default** (realized by returning Functions, instead of values).
6. **Pluggable “context” as native modules** and can use `require` syntax to consume them.
7. **(Optional) File-based module system** (still support `import`).
   Basically, it will be a **subset** of JavaScript.

### By fork-able

We want to create a hosted online language runtime for educational purpose where people can fork the project and experiment or replace any part of this runtime system.

![You can experiment with this runtime whatever you like!](/img/posts/runtime/architecture.png)

### By understandable

We plan to visualize runtime behaviors in the browser while your program is being executed because most of time, you don’t understand what’s going under the hood and through this visualization, you can know better about the mechanism of the runtime system.

![You can see what’s going on when your program is being executed.](/img/posts/runtime/visualization.png)

### Our Architecture

To have a good grip on the full picture, here is the architecture diagram of our planned runtime system:

![Runtime architecture](/img/posts/runtime/runtime-architecture.png)

We don’t have enough time to implement every single part, so we made use of some 3rd party libraries for some modules and focused on others we have more interests in.
You may want to know some glossaries and modules used inside the diagram:

### Guest Language

It’s the programming language of your program you plan to execute on your runtime system. In our case, it’s JavaScript.

### Host Language

It’s the programming language you plan to implement the runtime system. In our case, it’s Golang.

### Parser

To let runtime process your program written in guest language, you first need a parser to understand the syntax and grammar (syntax rules) of the guest language. I won’t get into details of how parser works but I found this article quite helpful if you’re interested in digging into what’s going on inside a parser. In short, your program will be parsed into a tree structure, which is called Abstract Syntax Tree (AST), and the purpose is to make interpreter easier to execute your program.
For parser, we use otto#parser out of box to parse JavaScript program and build AST for us.

### Interpreter

The interpreter will walk through the AST given by the parser, recursively visit each node, call corresponding evaluation function and use allocator to allocate memories from the heap to store data in your program. All in all, the interpreter will execute the program.
For interpreter, we’ll utilize the most of otto library as well, but due to the fact that we have to allocate the memory on our own, so we’ll modify some parts of interpreting process.

### Just-In-Time compiler (JIT)

Just-In-Time compiler will monitor the interpreting process and improve its efficiency. We skip the implementation for the phase one, because the main usage of it is to optimize the performance of compilation process and we probably don’t need that for educational purpose (Of course, how to implement a JIT compiler is still a quite interesting topic I think).If you want to know more on how it works, I recommend that you can start from this article written by Lin Clark: A crash course in just-in-time (JIT) compilers.

### Heap

A heap is a large region of your computer’s memory to store the data used in your program. Normally, a runtime system uses Stack memory to handle static data and Heap-based memory to handle dynamic data, but to make it simpler for our implementation, we only allocate memory from the heap. Inside the heap, we divide memories into fixed numbers of regions (which will be used for garbage collection) and we define the memory size for each region, primitive types and compound types. Moreover, it also contains some methods to create a new memory region, write data into the memory, read data from the memory…so on and so forth.

### Allocator

Along the way of interpreting process, the allocator will allocate memory for things like declaring and initializing variables. It will do so by consuming the methods provided by the Heap module. Besides, the allocator will try to re-use the region of memory it keeps. If there is no enough empty regions, it will ask Heap for more.

### Garbage Collector

Garbage collector is a mechanism on when and how we should recycle regions of our memory. There are different levels of garbage collection (minor GC, major GC and full GC), depending on current memory usage. For example, before a new region of memory is created by the heap, it may trigger a minor GC if there is no available region and if minor GC is not enough, a major GC or full GC will be triggered. Of course, there are way more details than described here, but just give you some basic idea.

### Runtime APIs

When writing a program, you sometimes need to do things that are quite common but out of programming language’s core features, for example, interacting with the file system. Take Node.js runtime as an example, it has fs native module, which provides an API for interacting with the file system, and is part of Node.js runtime APIs.

What’s mentioned above are pieces you will need to implement a runtime system. For sure, there are far more details than I described here, so if you’re interested in implementing one with us or on your own, just keep an eye on our open source project (https://github.com/pptang/goodtime/) and feel free to give us any suggestion or feedback!

Along the way, I’ll publish more articles explaining more fun challenges while implementing our runtime system🙂.
