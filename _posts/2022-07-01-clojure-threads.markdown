---
layout: post
title: Working with threads
date: 2022-07-01 01:00:00 +0300
description: Originally I was writing a post about Clojure state management when I realized there is one important topic I need to mention. Threads in Clojure.
img: threads/tp.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [clojure, tutorial]
discussion_id: 2022-07-01 
---

Originally I was writing a post about Clojure state management, when I realized, there is one important topic I need to mention. Threads in Clojure. I came into contact with threads in Clojure, when I worked with [quartzite](https://github.com/michaelklishin/quartzite) library .
It's very interesting topic, and I hope this post will help you understand threads and you'll enjoy it.

&nbsp;

### What is a thread

You can imagine thread as a serie of instructions to execute. One program can spawn many threads, and one thread can have many instructions. If we have one thread, we talk about <mark>Single threading</mark>, and if we have more than one thread, we call it <mark>Multithreading</mark>.

We can imagine program as a pacman game. Where pacman is a <mark>processor</mark>, pacman board is a <mark>program</mark>, one color is one <mark>thread</mark> and every single dot is an <mark>instruction</mark>.


![]({{site.baseurl}}/assets/img/threads/1.png#threads)

&nbsp;

### Single threading
_(Program with one thread)_


If we have a system with one processor (single-processor system) and program has only one thread (one line of dots), processor (pacman) is executing (eating) instructions one after another.

![]({{site.baseurl}}/assets/img/threads/2.png#threads)

So the program is executing instructions like this : 

![]({{site.baseurl}}/assets/img/threads/3.png#threads)

If we have system with more processors <mark>(multiprocessor system)</mark> , and only one thread , the instructions in thread are executed by only one processor. The second processor is not used.

&nbsp;

### Multithreading with single processor system

_(Program with multiple threads)_


If our program have more threads, processor is executing instructions concurrently. The processor switches back and forth between threads. It is important to know, the are no guarantees about switching back and forth between threads (When the program execution will be handed from one thread to another). 

The actuall thread execution can happen in multiple ways:

![]({{site.baseurl}}/assets/img/threads/4.png#threads)

This program can be executed:

#### Possible execution scenario 1

![]({{site.baseurl}}/assets/img/threads/5.png#threads)

#### Possible execution scenario 2

![]({{site.baseurl}}/assets/img/threads/6.png#threads)

>NOTE: Remember,there is no guarantee in which order instructions will be executed, so there are multiple possibilities, and we showed only two of them. It means, the program execution order is <mark>nondeterministic</mark>.


&nbsp;
### Multithreading with multiprocessor system

_(Multiple threads on hardware with more than one processor)_

Multiprocessor system allows the program to execute more than one instruction at the same time. System with two processors can execute two instructions synchronously.

![]({{site.baseurl}}/assets/img/threads/7.png#threads)

This example can be executed: 

![]({{site.baseurl}}/assets/img/threads/8.png#threads)


&nbsp;

### How you can start a new thread in Clojure

In Clojure you can create new thread using java class `Thread.` - by calling `(.Thread (my-fn))`. You need to give to thread a function without arguments. Then you can start thread using `.start` 

I will show you simple example of starting new thread.

We have functon named `make-fn` . This funtion takes one argument `text` and as a result returns an anonymous function without an argument. This anonymous function is looping function, and will print numbers from 1 - 10, prepended by `text` (argument from the outer function - as you can see, we are utilizing [closures](../closure-in-clojure) ).

<script src="https://gist.github.com/d91b2a60f86c7a5d3c3a.js"></script>


If we will call make-fn function with `from-thread` as an argument, function returns:

<script src="https://gist.github.com/820d9492580b0ccee18a.js"></script>

We define new function `run-in-two-threads!`. This function will set our looping function to be executed in two different threads. We call `make-fn` function with argument "thread-1" for the first thread, and for the second thread we call `make-fn` with an argument "thread-2"

<script src="https://gist.github.com/827baf720061f0ddf3ca.js"></script>

Calling `run-in-two-threads!` function:

<script src="https://gist.github.com/cde02575a16ca3c6eaa1.js"></script>

This is only simple explanation of threading principles. Off course threading is a big topic, because there are a lot of ways how you can manage threads, processors, etc. 
If you want to know something more, or you think there is something important I forgot to mention, feel free to write a comment.


