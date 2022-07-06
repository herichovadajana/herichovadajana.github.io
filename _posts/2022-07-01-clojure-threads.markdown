---
layout: post
title: Threads in Clojure
date: 2022-07-01 01:00:00 +0300
description: Originally I was writing a post about Clojure state management when I realized there is one important topic I need to mention. Threads in Clojure.
img: clojure.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [clojure, tutorial]
---

Originally I was writing a post about Clojure state management when I realized, there is one important topic I need to mention. Threads in Clojure. I came into contact with threads in Clojure, when I worked with quartzite library .
This time I found out, it's really interesting topic, and I hope, this post will help you understand threads, and you'll enjoy it.

What is a thread

You can imagine thread as a serie of instructions to execute. One program can spawn many threads, and one thread can have many instructions. If we have one thread, we talk about Single threading, and if we have more than one thread, we call it Multithreading

We can imagine program as a pacman game. Where pacman is a processor, pacman board is a program, one color is one thread and instead of dot is an instruction
