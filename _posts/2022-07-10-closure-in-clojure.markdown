---
layout: post
title: Closures in Clojure
date: 2022-07-10 01:00:00 +0300
description: The term Closure comes from fact that the code snippet (code block), has a free variables that can not be used or called from outside.
img: closure/tp1.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [clojure, tutorial]
discussion_id: 2022-07-10
---

When you check an official closure definition, it looks pretty complicated and confusing. But in reality, it is such a nice topic that can be explained very easily.

To figure out closure, we first need to explain what the terms <mark>outer function</mark>, <mark>inner function</mark>, and <mark>lexical scope</mark> mean.

As an example, we will use the `eat-a-cookie` function that will count how many cookies we ate, and store this information in an atom.

So our function looks like this:
<script src="https://gist.github.com/herichovadajana/96ff93c39e3606d189f5a8568b519373.js"></script>

#### Inner function

An inner function  is a function nested inside another function. In our example anonymous function.

> `(fn [cookies-i-ate-now]..` is nested inside `(def eat-a-cookie ..)` function


#### Outer function

An outer function is a function (perhaps not surprisingly :D ) on the "outside" of an inner function.

> `(def eat-a-cookie ..)` is on the outside of the nested `(fn [cookies-i-ate-now] ..` function

#### Lexical scope

<mark>Scope</mark> defines an area where variables (in Clojure, we call them <mark>bindings</mark> and they are immutable) are available. The availability of binding is defined within its context, or we can say within the expression (fn, let block) they are defined in.

>
````clojure
(fn [cookies-i-ate-now]
 (str "You ate " (swap! all-cookies-i-ate #(+ cookies-i-ate-now %)) " cookies"))
```

> the `cookies-i-ate-now` argument is available only within the anonymous function (<mark> scope </mark>) it's defined in.

Why are they important? Just imagine if there weren't any scopes. It would be a giant mess because then every binding would be global. If you would want to use for example an anonymous function `(fn [x] ..` it could be interfering with another piece of code where binding `[x]` is used (e.g another anonymous function `(fn [x] ..)` ).

The <mark>lexical</mark> part means that you can derive the scope from reading the source code.

### Now, what is a closure?


The term closure comes from the ability of the <mark>inner code block</mark> (in our case inner function) to access the bindings from the <mark>outer code block</mark> (in our case outer function) which "closes over" it.  

>We can say that "child" (inner) code block can always access bindings of the "parent" (outer) code block.

What happened when we called `(eat-a-cookie 2)`

<script src="https://gist.github.com/herichovadajana/7826a30918e3d72936ce49edc0f8c6d6.js"></script>

When we call `(eat-a-cookie 2)` with argument `2`, this argument is passed into our inner function `(fn [cookies-i-ate-now]) ..` and updates the value of the atom which is created in our outer function .

>
````Clojure
(def eat-a-cookie           
  (let [all-cookies-i-ate (atom 0)]
  ...
```

So our function `(fn (cookies-i-ate-now)..` has an access to the the atom which is defined in the scope of the outer function.

#### Example 2 (function that returns function) {#example}
_Here  we will write a function that tells us, how many cookies are left after we ate some :cookie:_

<script src="https://gist.github.com/herichovadajana/62f491a65a8944e1f034a2aaec13d1ae.js"></script>

<script src="https://gist.github.com/herichovadajana/b4b34a514bf4536a8ec918f8d42716ac.js"></script>


Let's take apart this example:

`(fn [cookies-eaten] ... )` is an inner function within the `(defn cookies .. )`  outer function.

When we call `(defn cookies [cookies-baked] ... )` it returns a function. So we call function `cookies` with an argument 10 (because let's say we baked 10 cookies).

>_So if we call `(cookies 10)` it will return a function that looks like this:_

>
````clojure
(fn [cookies-eaten]
  (let [cookies-left (- 10 cookies-eaten)]
...
```

And because `(cookies 10)` returns a function that takes `cookies-eaten` as an argument we will define this returned function as <br> `(def cookies-i-ate (cookies 10))` .

_Let's pretend we ate 5 cookies:_

<script src="https://gist.github.com/herichovadajana/9d8e1b623a846c32de1846834c282934.js"></script>

_9 cookies:_

<script src="https://gist.github.com/herichovadajana/5acaa92b9c1b223bdc20438d0cf7e99d.js"></script>

What happened here?

Basically, we put `5` as an argument into the inner `(fn [cookies-eaten]` function.

>
````
(cookies-i-ate (fn [5]
    (let [cookies-left (- 10 5)
...
```

So as you can see, our inner function `(cookies-i-ate ..)` has an access to the binding `cookies-baked` (defined in outer function) which has a value of 10 in our particular case. 

And this is called closure :)
