---
date: 2024-03-03T22:41:01
updatedAt: 2024-09-22 16:32:10
tags:
  - hugo_blog
  - Coroutine
  - Concept
  - JVM
categories:
  - Coroutine
  - Coroutine-series
series: "11"
aliases: 
aaa:aaa:aa: 
title: 0-Coroutine
lastmod: 2024-09-22T07:32:21.040Z
links: 
  - title: 123
  - description: 123
---
* 작은 쓰레드, 하나의 쓰레드를 어떻게 효율적으로 처리할것인가에 대한 방안 중 하나
* block작업(io요청)이 발생했을때, thread를 block하지않고 해당 작업을 suspend시키고 다른작업을 처리함

> Asynchronous or non-blocking programming is an important part of the development landscape. When creating server-side, desktop, or mobile applications, it's important to provide an experience that is not only fluid from the user's perspective, but also scalable when needed.
>
> Kotlin solves this problem in a flexible way by providing coroutine support at the language level and delegating most of the functionality to libraries.
>
> In addition to opening the doors to asynchronous programming, coroutines also provide a wealth of other possibilities, such as concurrency and actors.

* https://kotlinlang.org/docs/coroutines-overview.html
