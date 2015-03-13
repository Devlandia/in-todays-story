---
layout: post
title: "Lein Ring Server running in the background"
date: 2015-03-11 08:30:00 -0700
comments: true
author: Fellipe Brito
categories: [shell, clojure]
---

_by [@fellipebrito](https://github.com/fellipebrito)_

 I've been working in → working on a study case that has as a main target to run two different servers at the same time - one for the backend and the other for the frontend. [front-back-case](https://github.com/fellipebrito/front-back-case).

In order to not lose time and type less, I've been creating scenarios for every back/front server combination. So I can start and kill them with only one line. [scripts to start and kill servers](https://github.com/fellipebrito/front-back-case/tree/master/bin)

Today, after I code a simple server in Clojure, I had to figure out how to start a ring server in the background. After few minutes looking for a solution I realised that I could do it using nohup:
```
nohup lein ring server-headless 8080 >/dev/null 2>&1 &
