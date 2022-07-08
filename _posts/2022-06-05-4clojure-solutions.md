---
layout: post
title: 4clojure solutions
date: 2022-06-28 01:00:00 +0300
description: My solutions for 4clojure exercises.
img: clojure.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [clojure, tutorial]
discussion_id: 2022-06-28
---

My solutions for 4clojure exercises.

List is not complete yet, because I'm still working on it.

# 4clojure solutions

##### Problem 1
Nothing but the Truth

```clojure
(= 1 1)
```

##### Problem 2
Simple Math

```clojure
4
```

##### Problem 3
Intro to Strings

```clojure
"HELLO WORLD"
```

##### Problem 4
Intro to Lists

```clojure
:a :b :c
```

##### Problem 5
Lists: conj

```clojure
'(1 2 3 4)
```

##### Problem 6
Intro to Vectors

```clojure
:a :b :c
```

##### Problem 7
Vectors: conj

```clojure
[1 2 3 4]
```

##### Problem 8
Intro to Sets

```clojure
#{:a :b :c :d}
```

##### Problem 9
Sets: conj

```clojure
2
```

##### Problem 10
Intro to Maps

```clojure
20
```

##### Problem 11
Maps: conj

```clojure
[:b 2]
```

##### Problem 12
Intro to Sequences

```clojure
3
```

##### Problem 13
Sequences: rest

```clojure
'(20 30 40)
```

##### Problem 14
Intro to Functions

```clojure
8
```

##### Problem 15
Double Down

```clojure
#(* 2 %)
```

##### Problem 16
Hello World

```clojure
#(str "Hello, " % "!")
```

##### Problem 17
Sequences: map

```clojure
'(6 7 8)
```

##### Problem 18
Sequences: filter

```clojure
'(6 7)
```

##### Problem 19
Last Element

```clojure
#(first (reverse %))
```

##### Problem 20
Penultimate Element

```clojure
#(second (reverse %))
```

##### Problem 21
Nth Element

```clojure
#(get (vec %1) %2)
```

##### Problem 22
Count a Sequence

```clojure
#(reduce (fn [acc x]
           (inc acc))
         0 %)
```

##### Problem 23
Reverse a Sequence

```clojure
#(reduce (fn [acc x]
           (cons x acc))
         (empty %)
         %)
```

##### Problem 24
Sum It All Up

```clojure
#(apply + %)
```

##### Problem 25
Find the odd numbers

```clojure
#(reverse (reduce (fn [acc x]
                    (if (odd? x)
                      (conj acc x)
                      acc))
                  '() %))
```

##### Problem 26
Fibonacci Sequence

```clojure
#(loop [x [1 1]]
   (if (= (count x) %)
     x
     (recur (conj x (apply + (take 2 (reverse x)))))))
```


##### Problem 27
Palindrome Detector

```clojure
(if (string? %)
  (= (clojure.string/reverse %) %)
  (= (reverse %) %))
```

##### Problem 28
Flatten a Sequence

```clojure
#(reverse (reduce
           (fn rec-flatten [acc item]
             (if (coll? item) (reduce rec-flatten acc item)
                 (conj acc item)))
           '()
           %))
```

##### Problem 29
Get the Caps

```clojure
#(apply str(filter (set (map char (range 65 91))) %))
```

##### Problem 30
Compress a Sequence

```clojure
#(map first (partition-by identity %))
```

##### Problem 31
Pack a Sequence

```clojure
#(partition-by identity %)
```

##### Problem 32
Duplicate a Sequence

```clojure
#(seq (reduce (fn [acc item]
                (-> acc
                    (conj item)
                    (conj item)))
              [] %))
```

##### Problem 33
Replicate a Sequence

```clojure
#(mapcat (fn [item]
           (take %2 (repeat item)))
         %1)
```

##### Problem 34
Implement range

```clojure
#(take (- %2 %1) (iterate inc %1))
```

##### Problem 35
Local bindings

```clojure
7
```

##### Problem 36
Let it Be

```clojure
[x 7 y 3 z 1]
```

##### Problem 37
Regular Expressions

```clojure
"ABC"
```

##### Problem 38
Maximum value

```clojure
(fn [& args]
  (last (sort args)))
```

##### Problem 39
Interleave Two Seqs

```clojure
#(flatten (map (fn [f s]
                 (conj '() s f))
               %1 %2))
```

##### Problem 40
Interpose a Seq

```clojure
#(take (- (* 2 (count %2)) 1)
       (interleave %2 (repeat %1)))
```

##### Problem 41
Drop Every Nth Item

```clojure
#(mapcat (fn [a]
           (take (- %2 1) a))
         (partition-all %2 %1))
```

##### Problem 42
Factorial Fun

```clojure
#(apply * (range 1 (+ 1 %)))
```

##### Problem 43
Reverse Interleave

```clojure
#(apply map (fn [& args]
              args)
        (partition %2 %1))
```

##### Problem 44
Rotate Sequence

```clojure
#(let [new-coll (if (pos? %1)
                  %2
                  (reverse %2))
       new-position (if (pos? %1)
                      %1
                      (+ %1 (* -2 %1)))
       real-position (mod new-position (count %2))
       rotated (flatten
                (reduce
                 (fn [[acc-x acc-y] [x y]]
                   (if (< x real-position)
                     [acc-x (conj acc-y y)]
                     [(conj acc-x y) acc-y]))
                 [[] []]
                 (map-indexed vector new-coll)))]
   (if (pos? %1)
     rotated
     (reverse rotated)))
```

##### Problem 45
Intro to Iterate

```clojure
'(1 4 7 10 13)
```

##### Problem 46
Flipping out

```clojure
#(fn [x y]
   (% y x))
```

##### Problem 47
Contain Yourself

```clojure
4
```

##### Problem 48
Intro to some

```clojure
6
```

##### Problem 49
Split a sequence

```clojure
#(vector (take %1 %2) (drop %1 %2))
```

##### Problem 50
Split by Type

```clojure
#(vals (group-by type %))
```

##### Problem 51
Advanced Destructuring

```clojure
[1 2 3 4 5]
```

##### Problem 52
Intro to some

```clojure
[c e]
```

#### Problem 53
Longest Increasing Sub-Seq

```clojure
(fn [coll]
  (let [longest-seq (->> (reduce (fn [acc item]
                                     (cond
                                         (= (ffirst acc) nil) (conj (rest acc) (conj (first acc) item))
                                         (= (inc (ffirst acc)) item) (conj (rest acc) (conj (first acc) item))
                                         :else (conj acc (list item))))
                                 '(())
                                 coll)
                         (sort-by count)
                         last
                         reverse
                         vec)]
     (if (> (count longest-seq) 1)
       longest-seq
       [])))
```

##### Problem 54
Partition a Sequence

```
(fn [par-num coll]
  (->> (reduce (fn [acc item]
                 (if (= (count (first acc)) par-num)
                   (conj acc (list item))
                   (conj (rest acc) (conj (first acc) item))))
               '(())
               coll)
       (filter #(= par-num (count %)))
       (map reverse)
       reverse))
```

##### Problem 55
Count Occurrences

```clojure
#(into (sorted-map)
       (map (fn [[k v]]
              {k (count v)})
            (group-by identity %)))
```

##### Problem 56
Find Distinct Items

```
(fn [coll]
  (reduce (fn [acc item]
            (if ((set acc) item)
              acc
              (conj acc item)))
          []
          coll))

```

##### Problem 57
Simple Recursion

```clojure
'(5 4 3 2 1)
```

##### Problem 58
Function Composition

```clojure
(fn my-comp [& fns]
  (fn [& args]
    (if (= (count fns) 1)
      (apply (first fns) args)
      ((apply my-comp (butlast fns))
       (apply (last fns) args)))))
```

##### Problem 61
Map Construction

```clojure
#(apply merge (map (fn [k v]
                     {k v})
                   %1
                   %2))
```

##### Problem 62
Re-implement Iterate

```clojure
(fn my-iterate [fnc x]
  (cons x (lazy-seq (my-iterate fnc (fnc x)))))
```

##### Problem 63
Group a Sequence

```clojure
#(apply merge-with concat
        (map (fn [item]
               (hash-map (%1 item) [item]))
             %2))
```

##### Problem 64
Intro to reduce

```clojure
+
```

##### Problem 66
Greatest Common Divisor

```clojure
#(loop [a (max %1 %2)
        b (min %1 %2)
        r (rem a b)]
   (if (= r 0)
     b
     (recur b r (rem b r))))
```

##### Problem 68
Recurring Theme

```clojure
[7 6 5 4 3]
```

##### Problem 71
Rearranging Code: ->

```clojure
last
```

##### Problem 72
Rearranging Code: ->>

```clojure
reduce +
```

##### Problem 81
Set Intersection

```clojure
#(apply sorted-set
        (filter
         (fn [x]
           (contains? %1 x))
         %2))
```

##### Problem 83
A Half-Truth

```clojure
(fn [& args]
  (and (contains? (set args) false)
       (contains? (set args) true)))
```

##### Problem 88
Symmetric Difference

```clojure
#(set (concat (apply disj %1 %2) (apply disj %2 %1)))
```

##### Problem 90
Cartesian Product

```clojure
#(set
  (for [x %1
        y %2]
    [x y]))
```

##### Problem 95
To Tree, or not to Tree

```clojure
(fn tree [coll]
  (let [inner-coll (first (filter coll? coll))
        is-binary (odd? (count (filter #(and (not= % true) (not= % false)) (flatten coll))))]
    (if (and is-binary (= (count inner-coll) 3))
      (tree inner-coll)
      is-binary)))
```

##### Problem 97
Pascal's Triangle

```clojure
(fn pascal [row]
  (map (comp last take)
       (reverse (range 1 (+ 1 row)))
       (take row (iterate (partial reductions +) (take row (repeat 1))))))
```

##### Problem 99
Product Digits

```clojure
#(->> (* %1 %2)
      str
      seq
      (map (comp read-string str)))
```

##### Problem 100
Least Common Multiple

```clojure
(fn [& args]
  (letfn [(gcd [x y]
            (if (= x y)
              x
              (if (< x y)
                (recur x (- y x))
                (recur y (- x y)))))
          (lcm [x y]
            (/ (* x y) (gcd x y)))]
    (reduce lcm args)))
```

##### Problem 107
Simple closures

```clojure
#(fn [y]
   (apply * (repeat % y)))
```

##### Problem 118
Re-implement Map

```clojure
(fn my-map [f s]
  (when (seq s)
    (cons (f (first s))
          (lazy-seq (my-map f (rest s))))))
```

##### Problem 120
Sum of square of digits

```clojure
(fn [seq]
  (-> (fn [item]
        (if (> 10 item)
          (< item (* item item))
          (< item (apply + (map (comp #(* % %) read-string str first)
                                (split-at 1 (str item)))))))
      (filter seq)
      count))
```

##### Problem 122
Read a binary number

```clojure
(fn [s]
  (apply + (map (fn [x]
                  (apply * (repeat x 2)))
                (keep-indexed #(if (= 1 %2) %1)
                              (mapv (comp read-string str) (reverse s))))))
```

##### Problem 126
Through the Looking Class

```clojure
java.lang.Class
```

#### Problem 128
Recognize Playing Cards

```clojure
#(let [cards-suits {"S" :spade
                    "H" :heart
                    "D" :diamond
                    "C" :club}
       cards-rank (zipmap
                   (conj (->> (range 2 10)
                              (map str)
                              (vec))
                         "T" "J" "Q" "K" "A")
                   (range 13))
       card-data (map str (seq %))
       suit (cards-suits (first card-data))
       rank (cards-rank (last card-data))]
   {:suit suit :rank rank})
```

##### Problem 134
A nil key

```clojure
(fn [k m]
  (and (contains? m k)
       (nil? (m k))))
```

##### Problem 135
Infix Calculator

```clojure
(fn infix-calculator [& args]
  (let [[first-operand operator second-operand] (take 3 args)
        result (operator first-operand second-operand)]
    (if (= (count args) 3)
      result
      (apply infix-calculator (cons result (drop 3 args))))))
```

##### Problem 143
dot product

```clojure
#(apply + (map * %1 %2))
```

##### Problem 145
For the win

```clojure
'(1 5 9 13 17 21 25 29 33 37)
```

##### Problem 156
Map Defaults

```clojure
#(zipmap %2 (repeat %1))
```

##### Problem 157
Indexing Sequences

```clojure
#(map-indexed (fn [itm idx]
                [idx itm])%)
```

##### Problem 161
Subset and Superset

```clojure
#{1 2}
```

##### Problem 162
Logical falsity and truth

```clojure
1
```

##### Problem 166
Comparisons

```clojure
#(cond
   (%1 %2 %3) :lt
   (%1 %3 %2) :gt
   :else :eq)
```
