---
layout: post
title: Understanding recursion
date: 2022-06-15 01:00:00 +0300
description: First, let's explain what is recursion. Simply, recursion allows function to call itself.  
img: recursion/tp.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [clojure, tutorial]
discussion_id: 2022-06-15
---

### First let's explain what is recursion:

Recursion allows function to call itself. 

There are two types of recursion. The first one is <mark> tail recursion </mark> (iterative process written in recursive way) and the second way is <mark> real recursion </mark> (recursive process, which can be written only in recursive way).

### Difference between iterative process and recursive process

In the iterative process, the function arguments provide a complete description of the process state. If we would want to stop the program in the middle, and we want to resume it later, we could do it just from the information captured in function variables. 
However, in the recursive process, information is also maintained in the call stack, therefore "hidden" to the particular function call.

#### Let's see the difference on 'Boxes problem' solution 

We are moving frome one house to another, so we need to pack all our things to boxes. We have five boxes, but the boxes are stored like this: the smallest box is in a bigger box, and this box is in a little more bigger box..e.t.c. (like a [matryoshka doll](https://en.wikipedia.org/wiki/Matryoshka_doll).) So we need to know how much space in boxes we have for our things. 
The best way how to find out how much space we have, is by measuring volumes of all boxes. 
We have our data in following structure (the box is a cube) 
`{:a 5 :inner-box {:a 4 :inner-box {:a 3 :inner-box {:a 2 :inner-box {:a 1}}}}}`

What we need to do

1. Open the first box
2. Find out if there is another box inside
3. If there is another box, so we need to repeat point 1 and 2. (open the box and find out if there is another box inside). But we need to count of their volumes, and in this step will become the difference between interative and recursive process.


### Real recursion

_(recursive process)_

Solving boxes problem recursive way

<script src="https://gist.github.com/herichovadajana/8cf0c52dfe275224b746.js"></script>

Our function takes one argument - box 

As a first step, we select the `:a` key from arguments. `:a` is the lenght of the one edge of a box. Because a box is a cube, we can compute volume of this cube as `(* a a a)`. 

Next we need to find out if there is another box inside `(contains? box :inner-box)` 

If there is no box inside, fuction just returns volume of a box `volume-of-a-box` 

If there is a box inside, we will call the function another time, but we need to add the volume of the previous box `(+ volume-of-a-box (boxes-problem (:inner-box box)))` 

#### So what happens if we call this function?

<script src="https://gist.github.com/herichovadajana/f14f5e630920521107bb.js"></script>

The volume of the every previous box is maintained in call stack `(+ 125 (+ 64 (+ 27 ..)))` So those intermediate results are not maintaned in the function arguments.

Result

<script src="https://gist.github.com/herichovadajana/32f3cc1b9085273af419.js"></script>

### Tail recursion

_(iterative process)_

Iterative solution of the same problem (boxes problem).

<script src="https://gist.github.com/herichovadajana/ad99b01179b94fd0f721.js"></script>

<mark>First difference</mark> is, that our function takes 2 arguments. First argument is the summed volume of previous boxes (when we call this function for the first time it's 0, because we didn't compute any volume yet), and the second argument is box we need to compute volume for. 

The first few steps are the same as steps in recursive function. 
As a first step, we select the :a key from arguments. `:a` is the lenght of the one edge of a box. Because box is a cube, we can compute volume of this cube as `(* a a a)`.
Next we need to find out if there is another box inside `(contains? box :inner-box)`. 

<mark>Second difference</mark> 
If there is no box inside, fuction just returns volume of the box plus volume of the previous boxes. 
If there is a box inside, we will call `recur` (can be called only from tail position) with the count of the previous boxes volume as a first argument `(+ volume volume-of-a-box)`, and the second argument is inner box `(:inner-box box)` 

#### So what happens if we will call this function?

<script src="https://gist.github.com/herichovadajana/62780dd0143f7d8f732e.js"></script>

The main advantage of this (iterative) solution is, that the function arguments provide a complete description of the state. So in every new call, function knows the count of the previous boxes values. Function doesn't uses call stack to accomplish it's task.

Result

<script src="https://gist.github.com/herichovadajana/32f3cc1b9085273af419.js"></script>

### Summary

I personally prefer to express computation process in an iterative way, whenever it's possible (like the example above). Because with every recursive call, stack size increases. It could potentially crash the stack if there are too many recursive calls. There is no such limitation with iterative process written in a tail-recursive way (with recur).
