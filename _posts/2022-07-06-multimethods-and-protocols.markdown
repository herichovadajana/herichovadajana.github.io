---
layout: post
title: Multimethods and Protocols
date: 2022-07-06 01:00:00 +0300
description: In Clojure, and in functional languages in general, there are multiple ways to adapt functions to various types (in our case it will be animals). If we need to adapt function to various types, we usually use conditions (if, cond..), and if we want to add new behavior for another type, we need to touch part of code where types are distinguished. 
img: clojure.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [clojure, tutorial]
---

In Clojure, and in functional languages in general, there are multiple ways to adapt functions to various input types (in our case it will be animals). If we need to adapt function to different input types, we usually use conditions (if, cond..), and if we want to add new behavior for new input type, we need to touch part of code where types are distinguished. 

For example, I have a function

<script src="https://gist.github.com/46947964f83e15e6a270.js"></script>


Results

<script src="https://gist.github.com/8917852c3dcf472e88fb.js"></script>

<script src="https://gist.github.com/1dc7aac1f416e9d342f3.js"></script>


and I decided to find out something about Pumpkin and adopt him

<script src="https://gist.github.com/5cd35c3b34f1b0cb1014.js"></script>

I need to add a map representing pumpkin, add that into map of animals and modify `tell-me-about-animal` function (sound the animal makes is located in this function because all cats make the same sound, so it doesn't make sense to write it with every new cat, or dog .. )

<script src="https://gist.github.com/4bcda1e07eff41a1e9a2.js"></script>

Result

<script src="https://gist.github.com/d452da45dcae3f71a732.js"></script>


# Multimethods

In this case, using multimethods makes our code more extensible, because when adding a new animal type, we don't need to touch the `tell-me-about-animal` core function, which could be for example located in a library, and it would be difficult/impossible to modify it. So to add a new animal type, we just add a new case to our multimethod (defmethod).


<script src="https://gist.github.com/a8a1aab71011a91a7f32.js"></script>

Results

<script src="https://gist.github.com/3ab30420559b9dec711c.js"></script>

<script src="https://gist.github.com/342bb6c1a4f9637e41f3.js"></script>

<mark> defmulti</mark> creates a new multimethod with dispatch function. Therefore we create multimethod with name `animal-sound` and dispatch function in this multimethod selects keyword `:species` from argument.

<script src="https://gist.github.com/a4d41601172b0c7b4c0c.js"></script>

<mark>defmethod</mark> creates and installs a new method for mutimethod associated with dispatch value. `defmethod animal-sound` creates a new method for `(defmulti animal-sound (fn [my-animal] (:species my-animal))`.

We created three methods: 

#### First method

<script src="https://gist.github.com/43297e0e6b72898e9d98.js"></script>

is called, if returned value from `defmulti animal-sound` dispatching function is `:cat`

#### Second method

<script src="https://gist.github.com/6d9719e15ffedf7f2cbf.js"></script>

is called, if returned value from `defmulti animal-sound` dispatching function is `:dog`

#### Third method
     
<script src="https://gist.github.com/1c4761f70b60dd3022b0.js"></script>

is called, if returned value doesn't match any of the others methods. 

&nbsp;

>So as you can see, by calling `(animal-sound {:name "Twiggi" :species :dog :home "house" :age 3})` the dispatch function returns value under the keyword `:species` , which is `:dog` , and the appropriate method is used. Therefore returned value is `"Woof"`

><script src="https://gist.github.com/9ce1d3597716ba311a2a.js"></script>

&nbsp;

#### What if we want to add Pumpkin?

It's pretty simple. We just add pumpkin into `my-animals` map, and create a new method for species `:pig`. With every new animal specie we need to create only a new method. We don't have to dispatch only on keyword value as we do now, the dispatching logic can be much more complicated if you wish (i.e we can dispatch on sum of some arguments, first letters from some string, e.t.c).

<script src="https://gist.github.com/1236ce8c81fbbf0cd591.js"></script>

Result

<script src="https://gist.github.com/d452da45dcae3f71a732.js"></script>

# Protocols

Since protocols are faster then multimethods, is better use protocols when they are sufficient. In our case, protocols are good enough as we need to dispatch only on type. But when the dispatching logic gets more complicated, using multimethods is the way to go.

#### Create protocol

<script src="https://gist.github.com/05d7afaef6a4c0b787f1.js"></script>
            
<mark>defprotocol</mark> takes a name and an optional docstring. Method signatures contain method name `(sound)` , argument specification `([this])` , and an optional docstring `("animal language")` . When we want to implement protocol, we always need to provide implementation for all protocol methods.
            
<mark> reify</mark> creates a unique anonymous type. It is useful when you need to create single implementation of protocol, which doesn't have a named type.

<script src="https://gist.github.com/e9bbb833a6a7ba9a7cf3.js"></script>

we created one anonymous instance, which implements protocol `IAnimal` with all protocol methods.

On this instance `yeti` we can call all protocol methods.

Result

<script src="https://gist.github.com/c1eb0c78b0f6edf5f60d.js"></script>
            

<mark> deftype</mark> creates a named type which implements a protocol.

<script src="https://gist.github.com/e4521ed8bb8046877f3b.js"></script>

`deftype Goat` creates new type which can take arguments (in our example arguments field is empty, because we don't use them) , followed by protocol name which we want to implement `(IAnimal)` and implementation of all protocol methods.

i.e. `(sound [this] "Meh")` is protocol implementation for `IAnimal` method `(sound [this] "animal language")`

`(def Buttermilk (Goat.))` constructs a new instance of type Goat.


Result

<script src="https://gist.github.com/762b69eab48d1e3a67d0.js"></script>

Now we can call method `sound` on instance `Buttermilk`

<mark> defrecord</mark> creates a named type which implements protocol, and additionaly behaves like a map for any arguments passed to constructor (where map keys are argument names, and map values are actual arguments passed when calling a constructor)

<script src="https://gist.github.com/f07bdba7c400a6871852.js"></script>

We defined new record of type `Cat`, with arguments `name` `home` `age`. For this record we implemented `IAnimal` with all the protocol methods.

Then we defined new instance of type cat assigned to `Siggi`, with arguments `"Siggy"` `"House"` `1`

When if we call `Siggi`, then `"Siggi" "House" 1` are passed as values for keywords `name home age` as defined in `defrecord Cat`.

Now we can call all the methods (sound, species) on instance `Siggi`

Result

<script src="https://gist.github.com/a33ebfe4104a7806f4bb.js"></script>

<script src="https://gist.github.com/d8df0573bf8ad0b3df18.js"></script>

You can work with them like with maps. You can call assoc, dissoc, keys e.t.c on them, although you have to be careful with dissoc, as it returns plain map instead of record. 

<script src="https://gist.github.com/2e5bd6c0d7f31e3e73e0.js"></script>

#### Take a look at our example with protocols

<script src="https://gist.github.com/b5e7864f16fcbf817e31.js"></script>

Results

<script src="https://gist.github.com/8917852c3dcf472e88fb.js"></script>

<script src="https://gist.github.com/1dc7aac1f416e9d342f3.js"></script>

We add Pumpkin:

<script src="https://gist.github.com/d92618bb44729c1bf430.js"></script>

So for our pig Pumpkin to join other animals, we needed to add Pumpkin into `my-animals` map , and create new defrecord for species pig.
  
Result

<script src="https://gist.github.com/5c3f4cdf56bd921473d3.js"></script>

The end &nbsp; :pig:

