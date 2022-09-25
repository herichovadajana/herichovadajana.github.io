---
layout: post
title: Snake game -  a tutorial for ClojureScript
date: 2022-09-19 01:00:00 +0300
description: Simple tutorial on how to create Snake game using ClojureScript, reagent and re-frame
img: sg.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [clojure, tutorial]
discussion_id: 2022-09-19
---

I was thinking about writing a tutorial to show people how to create a simple interactive application using ClojureScript/re-frame. 
And then, I realized that an arcade game would be a great example. One of the first games I played was Snake. Most often secretly under the desk during classes in school on my old Nokia 3310 :)

So I decided to make a virtue of necessity, and the result is this tutorial.

The first part of our tutorial has three goals:

1. [Setting up the project](#1)
2. [Creating the data model](#2)
3. [Implementing the static view](#3)
4. [Make the snake move](#4)
5. [Actions to perform when the food item is caught](#5)
6. [Collisions](#6)
7. [Game over and replay](#7)

## <a name="1"></a> Setting up the project

Download and install [leiningen](http://leiningen.org/).

Create the project by running `lein new figwheel-main snake-game -- --reagent` in your console.

In your [project.clj](https://github.com/Lambda-X/snake-game/blob/v1.0/project.clj#L10#L11) add to `:dependencies` re-frame `[re-frame "1.3.0"]`.

We will need to have four namespaces. We already have `core.cljs` so we will create `handlers.cljs` `utils.cljs` `view.cljs`. 

Don't forget to put namespace at the top of your 

`handlers.cljs` file
<script src="https://gist.github.com/366294e51f179a3c6c6462318f106585.js"></script>

`view.cljs` file

<script src="https://gist.github.com/5db1383419a5306555be3b0837e5b4c5.js"></script>

`utils.cljs` file

<script src="https://gist.github.com/c93d34739946f080c5b21f2d3e29dc74.js"></script>

Also we will delete everything we have in `core.cljs` so that it will look like this

<script src="https://gist.github.com/1c80724365affadbc05e8c89af402a5b.js"></script>

And the same with `core_test.cljs` (test/snake_game/core_test.cljs). We have to do this. Otherwise, we can have a problem with compiling. The template is rendered with written tests that don't match our project.

<script src="https://gist.github.com/56d6f0b69c3a45eeb9585b0611512ba6.js"></script>

You also need a CSS file to set up some style.
So you can copy it from [here](https://github.com/herichovadajana/snake-game/blob/main/resources/public/css/style.css) and paste it to `resources/public/css/style.css`.

Run `lein fig:build`, open [localhost:9500](http://localhost:9500/) in your browser, and you should see that your HTML and CSS were loaded (gray background).

## <a name="2"></a> Creating the data model

Rewrite your `handlers.cljs` (`src/snake_game/handlers.cljs`) to look like below. In this namespace we will register all our events and subscriptions handlers.
We will also require snake-game.utils namespace, as there we will have all our utility functions.

<script src="https://gist.github.com/35c39e99abca511b0036.js"></script>

So, it's time to create our data model.
Let's think about what we need.

1. We need a board where the snake will move
2. We need the snake (its body position and head direction)
3. The food item
4. The score
5. The game state (playing or game-over)

**The initial state**

- All the needed data will be stored in the `initial-state` map.

<script src="https://gist.github.com/64cd34c4d8550ba208a5.js"></script>

In the beginning, every map value is `nil`. So let's fill the map with our data.

**The board**

- The `:board` has two values: `x` (width) and `y` (height). Our board's width is 35 and height 25.

<script src="https://gist.github.com/b971b37e6259893eee1b.js"></script>

**The snake**

- We will store the snake's `:body` in a vector of vectors `[[3 2][2 2] ..]`. Every vector is a pair of cell coordinates for a particular body part.
- The `:direction` will be stored in a vector as well.

<script src="https://gist.github.com/d51b209f4fac547a0a46.js"></script>

**The food item**

- The food item's position will be stored in `:points` and should have a random position that does not collide with the snake's body.
We need to write a function that takes the snake and board as arguments and returns the first free random position. 
If there's no available position, the function will return `nil`. We will place this function in our `utils.cljs` namespace. 

<script src="https://gist.github.com/4ce13481641693d8168e.js"></script>

**The score**

- The default value of the score is 0, and it is stored in `:points`.

**The game state**

- When we start the game, the value of the `:game-running?` key is set to `true`, which means that the game is running.
It will become `false` when the game is stopped for any reason.

Now the `initial-state` is filled and looks like this:

<script src="https://gist.github.com/d61199d296561017f13f.js"></script>

## <a name="3"></a> Implementing the static view

Before explaining how we implemented the UI, we should talk a little bit about [re-frame](https://github.com/Day8/re-frame).
In re-frame, we make mutations to the application state (or simply `db`, the atom defined by `re-frame`) only through the so-called handlers.
For the sake of simplicity, we can perceive a handler as a function that takes as input the current application state with some additional arguments and returns a new application state.
This function is registered using a regular ClojureScript keyword (handler-identity). The only way to run it is to dispatch an event, where the event is a vector containing a handler-identity with some additional arguments.

We create handlers and register them by using `re-frame/reg-event-db`.

The `:initialize` handler is the first handler we need.
It will merge our `initial-state` into the application state.

<script src="https://gist.github.com/fcb001c1042d3811e09d.js"></script>

If you write `@re-frame.db/app-db` in your repl, you should see the application state.
But at this time, there's only an empty map.
That's because our `:initialize` handler hasn't been called yet.
We'll do this by using `:dispatch-sync` in our `core.cljs` namespace. But first we have to rewrite `core.cljs` to look like below. In this part we, are requiring everything we will need in `core.cljs`

<script src="https://gist.github.com/412d62f2787bd7ec7fb357fae81156bc.js"></script>

<script src="https://gist.github.com/fcdef3122bb98b4590d5.js"></script>

And now we just need to call the `run` function in our `snake-game.core` namespace.

<script src="https://gist.github.com/b2ce9cd323c5c5ba4d58.js"></script>

You can try `@re-frame.db/app-db` in the repl and you'll see that the application state is now:

```
{:board [35 25],
 :snake {:direction [1 0],
         :body [[3 2] [2 2] [1 2] [0 2]]},
 :food [15 13],
 :points 0,
 :game-running? true}
```

But in your browser, you still see nothing, right? So let's fix that.

We need to tell our application where we want to render our app, that is,
into the `div` with the id `app` in our HTML.
For this to happen, we need to use the `rdom/render` function.
This function takes a `reagent` component (it will be called `game` in our case) and a DOM node inside which the rendering will happen.
We can get the DOM node by using JavaScript interop `(.getElementById js/document "...")`.

We will modify our `run` function to render our component.

<script src="https://gist.github.com/4b624fdf87395cf04991.js"></script>

Let's create our `game` (main rendering) function. We will create it in the `snake-game.view` namespace.
This function creates an empty `div` this time.
To represent HTML elements, we use plain Clojure data structures (if you want to know more about this syntax, visit [Hiccup](https://github.com/weavejester/hiccup)).

<script src="https://gist.github.com/ba7219c8106a1f210746.js"></script>

If you open the developer console in your browser ([localhost:9500](http://localhost:9500/)) you'll see the new empty `div` inside the `div` with the id `app`.

It's time to render the board.
To get the board data from the app state, we'll need to register a subscription in the `snake-game.handlers` namespace.

A subscription is the `re-frame` way to change the UI state every time the `db` changes.
As with handlers, each subscription is registered using a keyword `:board` in this case.
This subscription function takes a `db` as an argument and any optional arguments.
In the body of the function, we will dereference the `db` and extract the `:board`  `(:board db)`.

<script src="https://gist.github.com/31da0bdb2b80e314178e.js"></script>

If you write `@(re-frame.core/subscribe [:board])` in your repl, you'll see the board value `[35 25]`. `reagent/subscribe` returns a `reagent/reaction`, which returns a deref-able object behaving like a read-only `ratom` (reactive atom).

Now we can connect it to our view.

We will add all require we will need to `snake-game.view` namespace.

<script src="https://gist.github.com/7ac39a7ad89dd17f2a9ba9afa696f9cd.js"></script>

In the `snake-game.view` namespace we will display our board as an HTML table by creating the `render-board` function.
As you can see, we are using a `subscribe` in our `let` block to get the `:board` value.

<script src="https://gist.github.com/fc8ac5bb14d7fc15f543.js"></script>

And we are calling it in our main rendering function.
We will render the game board inside the existing `div`.


<script src="https://gist.github.com/dd82f67fd533643678d2.js"></script>

After saving, you can see an empty board in your browser.

Now we'll modify the `render-board` function to show the snake and the food item as well.

To react to direction changes and the new food item location, we need to create a subscription in the `snake-game.handlers` namespace for the snake and the food item.

<script src="https://gist.github.com/1c09e5416579f0a6094b.js"></script>

And modify our `render-board` function. We will additionally `subscribe` the snake and the food item and, based on their indexes, use an appropriate CSS class on the table cell.

<script src="https://gist.github.com/c24ae08c282d647f9483.js"></script>

The score part is the simplest one of the view.
In the `snake-game.handlers` namespace, we create a subscription 

<script src="https://gist.github.com/6f3ef6ba51fe8d9dc5ec.js"></script>

And in the `snake-game.view` render the `render-score` function with a `div` where the score will be located.

<script src="https://gist.github.com/65399ecc1923875085fb37203a0018d8.js"></script>

And add the `render-score` function to the `game` function.

<script src="https://gist.github.com/d755f3faa376c30a8845.js"></script>

The last part of the view is the game-over overlay. This overlay appears when the `:game-running?` in the `app-state` is `false`.

I'm sure you know what's coming:
we need to create a new subscription in `snake-game.handlers` plus a new function in `snake-game.view`, which renders a `div` styled as an overlay.
This function will have a simple condition.
If the value of `:game-running?` is `true`, the function returns an empty `div`. Otherwise a `div` with the `class` attribute is set to `overlay` (all classes are defined in [resources/public/css/style.css](https://github.com/herichovadajana/snake-game/blob/main/resources/public/css/style.css)).
We will also render an `h1` with the symbol ↺ to provide a clickable target for refreshing the game.

So we will register a new subscription in `snake-game.handlers`

<script src="https://gist.github.com/dd2653694c594f5f9f4f.js"></script>

And create `game-over` function in `snake-game.view` namespace.

<script src="https://gist.github.com/551c94ccb98b37ad9d5d63c5d448254f.js"></script>

And modify the `game` function to render `game-over`.

<script src="https://gist.github.com/7a02f3006f50f5cf2c94.js"></script>

Now you can see the entire board with the snake, the food item, and the score in your browser.

![static-part]({{site.baseurl}}/assets/img/snake-game/static.png#snake-game)


If you change the value of the `:game-running?` to `false` in the `initial state`, you'll see a `game-over` overlay.

![static-game-over]({{site.baseurl}}/assets/img/snake-game/static-game-over.png#snake-game)

## <a name="4"></a> Make the snake move

To make the snake move, we need to update its data at regular intervals.
But first, we need to create a function that animates the snake.
This function will take the snake map as an argument and create a new one, in which
the snake's body is moved one step forward. We will place this function into `snake-game.utils` namespace.

<script src="https://gist.github.com/9cd7dfc668c7af2f0b28.js"></script>

In the `let` block, we define the new snake's head position by applying
the direction on the first segment (the snake head).
The new coordinates of the next segment are the old coordinates of the previous
segment.

We know that if we want to make mutations in our code, we need to use handlers.
We will register an event named `:next-state` and use our `move-snake` function
to update the snake.
Before calling `move-snake` in the `:next-state` event, we have to check
whether the game is still going on (the value of `:game-running?` in app-state).
Otherwise, we won't do anything and will return the unchanged `db`.

<script src="https://gist.github.com/ead3a55af334d8ddaf36.js"></script>

You can try `(dispatch [:next-state])` in your repl, and you'll see the snake is
rendered in the new position.
But we want to keep our snake moving, so we need to dispatch the `:next-state`
event periodically, for example, every 150ms.
This interval will reflect the "speed of the game" and hence also the
difficulty :)

To achieve this, we'll use JavaScript interop again. 

In the `snake-game.core` namespace

<script src="https://gist.github.com/8e40c0dc4460099d3901.js"></script>

The snake is moving, but now we need to control him.

We will listen for specific
[key codes](https://css-tricks.com/snippets/javascript/javascript-keycodes/):
_up_ (38), _down_ (40), _left_ (37), and _right_ (39) to determine which arrow
key was pressed.

So we want those keys to change the direction of the snake.
The natural way to express it would be to create just a mapping from key codes
to the corresponding direction vectors.

In the `snake-game.utils` namespace

<script src="https://gist.github.com/29b393eba05f7f6932b9.js"></script>

When the key is pressed, we'll look up the corresponding direction vector
from the `key-code->move` map, and then we'll update the snake with a new
direction.

<script src="https://gist.github.com/7f0e0440b1bc91f78c44.js"></script>

The key lookup and dispatch will be performed when the `key-down` event
is detected.

<script src="https://gist.github.com/f9482091ed78721327e7.js"></script>

But there's still one little problem.
If we press the arrow pointing to the opposite direction to the snake's
current one, the snake will collide with itself.
We can either consider this a collision and stop the game or ignore that move.
I chose the second option.

The `change-snake-direction` function takes a new direction and the current
one and compares them.
If this new direction is perpendicular to the old direction, the function
returns a new direction. Otherwise just the old direction.

in the `snake-game.utils` namespace
<script src="https://gist.github.com/0b1fe4352c2ec34997aa.js"></script>

Of course, we need to use this function in the `:change-direction` event.

<script src="https://gist.github.com/9a08ca320042a32ff6c8.js"></script>

Now we can fully control the snake's movement and the only things left are
collision handling and food item catching.

## <a name="5"></a> Actions to perform when the food item is caught

To create the food item in a new position, we don't need a new function. We
can re-use `rand-free-position`, but to make the snake longer, we need to create
a new function, `grow-snake`.

This function will use the last two parts of the snake and, based on those,
create another part at the end of the snake.
We'll also make a small helper function `snake-tail`, which will compute
`x` or `y` coordinate based on the last two values of the said coordinate in
the snake body.

For example, if this function gets `[1 2]` as arguments, it will return `3`,
if `[2 1]` it will return `0`.

In the `snake-game.utils`
<script src="https://gist.github.com/0d6ede73d35350319d6f.js"></script>

We will use it in our `grow-snake` function.
This function will take the snake as an argument, and based on the last two
coordinates of the snake body, it will create another coordinate and `conj`
it to the snake.

<script src="https://gist.github.com/857db40a6ba959797deb.js"></script>

To increase the score, we use the `inc` function on `:points` in db.

We will use the `process-move` function to put all three functions together.
This function will take a whole db as an argument, and if the first snake part
(the head of the snake) has the same coordinate as the food item, we will
update every key we need:
`(:snake (grow-snake) :points (inc) :food (rand-free-position))`

<script src="https://gist.github.com/bdee06cd01afb3abc87d.js"></script>

The best time to call the `process-move` function is in the `:next-state` handler,
chained after the `move-snake` call.

In the `snake-game.handlers` namespace
<script src="https://gist.github.com/4306ef43c2453faca05f.js"></script>

## <a name="6"></a> Collisions

Although we can play the snake game, it's impossible to make it end.
It's also very easy to make the snake go outside the board, but
nothing will happen.

We need to create two types of collision.
The first is when the snake collides with the border and the second one is when the
snake collides with its own body.
The collision functions will take the snake and the board as arguments and
return `true` if the collision occurred and `false` otherwise.

We need to realize that the next `x` index of the snake head (that is, the position
in which the snake head will be in the next state) can be neither `-1` nor
the width of the board incremented by `1`.
The next `y` index of the snake head can be neither `-1` nor the height
of the board incremented by `1`.
Similarly, the next head position `[x y]` can't be equal to any coordinate
of the snake body.

In the `snake-game.utils` namespace

<script src="https://gist.github.com/d735b421e3cb869778e4.js"></script>

We check for collisions in the `:next-state` event.
If a collision occurred, we return the existing `db`, with `:game-running?`
set to `false`. Otherwise, we call the next steps (`move-snake`, `process-move`).

<script src="https://gist.github.com/01b2a326c444352a4165.js"></script>

## <a name="7"></a> Game over and replay

If a collision occurs, the game over overlay will be rendered.
In this last step, we need to make the refresh icon clickable.
Clicking on this icon will reset the datastate. We can achieve this very easily
by dispatching the `:initialize` event that, as you probably remember,
merges `initial-state` into the app-state so that the state
of the game will be the same as when the game started.

In the `snake-game.view` namespace
<script src="https://gist.github.com/bd4971f71776dc61b763.js"></script>

And we can't forget to add `game-over` function into the `game` function. 
<script src="https://gist.github.com/be1d2f74a112cd41575819e6ce28bb9b.js"></script>

## The end

With the app-state so nicely encapsulated in one single place, it's very easy to
add features like changing the board size or pauseing the game. If you want to know
more about this style of programming and state management, I highly recommend reading
the [re-frame tutorial](https://github.com/Day8/re-frame).

Meanwhile, enjoy the game!

<script>
document.onkeydown = function(evt) {
    evt = evt || window.event;
    var keyCode = evt.keyCode;
    if (keyCode >= 37 && keyCode <= 40) {
        return false;
    }
};
</script>

<div id="app"></div>
<script src="../assets/snake-game/snake-game.js"></script>
<link rel="stylesheet" type="text/css" href="../assets/snake-game/style.css" />

The full source code is available on [GitHub](https://github.com/herichovadajana/snake-game).

## Links

- [https://github.com/Day8/re-frame](https://github.com/Day8/re-frame)
- [https://reagent-project.github.io/](https://reagent-project.github.io/)
- [https://github.com/weavejester/hiccup](https://github.com/weavejester/hiccup)

