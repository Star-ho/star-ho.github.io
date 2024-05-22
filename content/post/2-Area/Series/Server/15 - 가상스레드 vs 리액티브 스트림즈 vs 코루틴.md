---
date: 2024-03-31T22:41:00
updatedAt: 2024-04-21 18:32:05+2140
tags:
  - Server-History
  - hugo_blog
  - virtual-thread
  - Reactive
categories: Server-History
title: 15 - 가상스레드 vs 리액티브 스트림즈 vs 코루틴
lastmod: 2024-04-21T09:32:10.672Z
---
가상쓰레드, 리액티브 스트림즈, 코루틴 이 세가지 프로젝트는 모두 Blocking I/O로 인한 병목을 줄이기 위해 nonBlocking I/O를 사용할 목적으로 쓰이고 있습니다.

기존에는 리액티브 스트림즈, 코루틴으로 nonBlocking I/O를 사용했다면, 작년 가상쓰레드가 나온 이후, 과연 어떻게될 지에 대해 개인적인 의견 및 정보들을 작성하려 합니다.

우선 세가지 프로젝트의 목표에 대해 이야기 하려합니다.

## [리액티브 스트림즈](https://www.reactive-streams.org/)

`The main goal of Reactive Streams is to govern the exchange of stream data across an asynchronous boundary—think passing elements on to another thread or thread-pool—while ensuring that the receiving side is not forced to buffer arbitrary amounts of data. In other words, back pressure is an integral part of this model in order to allow the queues which mediate between threads to be bounded. The benefits of asynchronous processing would be negated if the communication of back pressure were synchronous (see also the [Reactive Manifesto](http://reactivemanifesto.org/)), therefore care has to be taken to mandate fully non-blocking and asynchronous behavior of all aspects of a Reactive Streams implementation.`

간단히 요약해보자면\
`비동기 뿐만이 아닌, 쓰레드간 element들을 교환하여 백프레셔를 지원하여 수신측에서 리소스를 지원한다`\
입니다

리액티브 스트림즈트는 비동기 뿐만이 아닌, 배압까지 신경쓰고 있는 것을 알 수 있습니다

## [코루틴](https://github.com/Kotlin/KEEP/blob/master/proposals/coroutines.md)

`No dependency on a particular implementation of Futures or other such rich library;
`Cover equally the "async/await" use case and "generator blocks";`
`Make it possible to utilize Kotlin coroutines as wrappers for different existing asynchronous APIs (such as Java NIO, different implementations of Futures, etc).\`

코루틴의 목표는`Futures와 다른 라이브러리 의존 없이, 비동기 api의 래퍼를 제공`하는것 입니다

## [가상 스레드](https://openjdk.org/jeps/444)

`Enable server applications written in the simple thread-per-request style to scale with near-optimal hardware utilization.`\
`Enable existing code that uses the java.lang.Thread API to adopt virtual threads with minimal change.`\
`Enable easy troubleshooting, debugging, and profiling of virtual threads with existing JDK tools.`

가상스레드의 목표는 `최소한의 변경으로, 현재 서버 애플리케이션이 작성된 요청당 스레드 모델의 최적화된 하드웨어 사용`이라는 것을 알 수 있습니다.\
\
추가로 Improving scalability with the asynchronous style을 살펴보면 reactive에 대한 내용이 더 나온다.

```
Some developers wishing to utilize hardware to its fullest have given up the thread-per-request style in favor of a thread-sharing style. Instead of handling a request on one thread from start to finish, request-handling code returns its thread to a pool when it waits for another I/O operation to complete so that the thread can service other requests. This fine-grained sharing of threads — in which code holds on to a thread only while it performs calculations, not while it waits for I/O — allows a high number of concurrent operations without consuming a high number of threads. While it removes the limitation on throughput imposed by the scarcity of OS threads, it comes at a high price: It requires what is known as an _asynchronous_ programming style, employing a separate set of I/O methods that do not wait for I/O operations to complete but rather, later on, signal their completion to a callback. Without a dedicated thread, developers must break down their request-handling logic into small stages, typically written as lambda expressions, and then compose them into a sequential pipeline with an API (see [CompletableFuture](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/CompletableFuture.html), for example, or so-called "reactive" frameworks). They thus forsake the language's basic sequential composition operators, such as loops and `try/catch` blocks.

In the asynchronous style, each stage of a request might execute on a different thread, and every thread runs stages belonging to different requests in an interleaved fashion. This has deep implications for understanding program behavior: Stack traces provide no usable context, debuggers cannot step through request-handling logic, and profilers cannot associate an operation's cost with its caller. Composing lambda expressions is manageable when using Java's [stream API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/stream/package-summary.html) to process data in a short pipeline but problematic when all of the request-handling code in an application must be written in this way. This programming style is at odds with the Java Platform because the application's unit of concurrency — the asynchronous pipeline — is no longer the platform's unit of concurrency.
```

리액티브 스타일의 어려운점(코드 스타일, 스택 트레이스, 디버깅 등)을 언급하는 부분이 있다.

```
Typically, a virtual thread will unmount when it blocks on I/O or some other blocking operation in the JDK, such as `BlockingQueue.take()`. When the blocking operation is ready to complete (e.g., bytes have been received on a socket), it submits the virtual thread back to the scheduler, which will mount the virtual thread on a carrier to resume execution.
```

추가로 virtual Thread는 Blocking작업을 해당 스레드 unmount를 통해 Blocking을 회피할 수 있는것도 알 수 있다.

## 결론

이를보면 가상 스레드가 리액티브 스트림즈를 대체하는 Blocking I/O로 인한 병목을 줄이는 기능을 하지않을까 싶다.

java reactor에서 virtual thread를 사용하는 scheduler를 추가하며 vitual thread를 지원한다.\
지원한다고 해도 reactive streams의 단점이 뚜렸다하기에 reactive streams의 추가적인 기능인 배압을 사용하지 않는 이상, virtual thread를 사용하는게 더 나을거란 생각이다.

coroutine에서도 가상스레드를 지원하지만, Coroutine 또한 코드의 변경이 필요하기에, 가상스레드가 더 나을거라 생각된다

개인적으로 리액티브 스트림즈 구현체인 리액터를 사용하면서 어려운점이 많았기에 얼른 가상스레드를 사용하고 싶다.\
하지만 가상스레드가 나왔지만, 데이터베이스 드라이버 같은 외부 라이브러리의 수정이 완성되지 않아 업무 프로젝트에는 사용하기 어려운점이 아쉽다.

https://www.reactive-streams.org/\
https://openjdk.org/jeps/444\
https://github.com/Kotlin/KEEP/blob/master/proposals/coroutines.md\
https://blog.honeybomb.kr/9\
https://perfectacle.github.io/2023/07/10/java-virtual-thread-vs-kotlin-coroutine/
