---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8550
tags:
  - hugo_blog
  - Coroutine
categories: Coroutine
title: 3-CoroutinDispatcher
lastmod: 2024-04-21T09:36:36.351Z
---
* dispatcher의 사전적 정의
  * 사람이나 차량, 특히 긴급 차량을 필요한 곳으로 보낼 책임이 있는 사람
* 코루틴에서는?
  * 코루틴을 실행시킬 thread를 결정
  * 코루틴을 실행시킬 쓰레드를 제한함
  * 쓰레드 풀로 dipath하거나, unconfined한 상태로 실행할 수 있음
* coroutine builder에서는 CoroutinContext를 optional하게 받는데 여기서 dispatcher를 인자로 받을 수 있음

> CoroutineDispatcher는 RxJava의 Scheduler와 유사함

## Default dispatcher

* 코루틴에서 아무것도 설정하지 않는다면 기본으로 제공되는 dispatcher
* CPU-intensive한 작업을 실행하기 위해 디자인됨
* 기본 thread개수는 최소 2개, 최대 cpu core수만큼 생성됨
  * 이론상 cpu-intensive한 작업을 하고, blocking하지 않는다고 가정하면 최적의 개수임

> ***limitedParallelism***\
> 하나의 무거운 코루틴에서 모든 DefaultDispatcher를 사용하면 다른 코루틴에서 사용할 DefaultDispatcher가 부족할 수 있음\
> limitedParallelism을 사용해서 현재 코루틴에서 사용할 쓰레드 수를 제한할 수 있음

## IO Dispatcher

* 파일 읽기/쓰기, 네트워크 요청과 같은 I/O작업에 사용하기 위해 만들어짐
* 코어의 수에 따라 다르지만, 최대 64개 thread를 생성
  * thread 개수가 무제한이라면 쓰레드를 계속 생산할것이고, 쓰레드를 생성/삭제하는 것도 비용이므로 적절한 thread 수를 관리해야함
  * 또한 thread가 무한정 생성하면 Out-Of-Memory가 발생

### Default Dispatcher와 IO Dispatcher를 함께쓴다면?

```kotlin
suspend fun main(): Unit = coroutineScope {
    launch(Dispatchers.Default) {
        println(Thread.currentThread().name)
        withContext(Dispatchers.IO) {
            println(Thread.currentThread().name)
        }
    }
}
```

* 풀을 공유하기에 Default Dispather안에서 IO Dispatcher를 사용한다고 redispatching이 발생하지 않음
* 하지만 서로의 limit은 공유하지않음
  * Default Dispather안에서 IO Dispatcher로 바꼈을때, thread는 변경되지 않지만, 서로가 서로를 고갈시키지 않기 위해, IO Dispathcer의 thread로 count함
* limit을 공유하지 않으므로, Default Dispather과 IO Dispatcher를 동시에 최대로 사용했을떄, 8코어 환경에서는 (Default Dispather의 개수(8) + IO Dispatcher(64)) 쓰레드풀에 총 72개의 쓰레드가 관리됨

### IO Dispatcher에서의 limitedParallelism

* IO Dispatcher에서 limitedParallelism는 다른 Dispatcher와 다르게 동작함
  * 새로운 독립된 쓰레드 풀을 가진 Dispatcher를 생성함
* 원하는 만큼, 64개보다 더 많은 쓰레드를 지정할 수 있음

```kotlin
import kotlinx.coroutines.*
import kotlin.system.measureTimeMillis

suspend fun main(): Unit = coroutineScope {
    launch {
        printCoroutinesTime(Dispatchers.IO)
        // Dispatchers.IO took: 2074
    }

    launch {
        val dispatcher = Dispatchers.IO
            .limitedParallelism(100)
        printCoroutinesTime(dispatcher)
        // LimitedDispatcher@XXX took: 1082
    }
}

​

suspend fun printCoroutinesTime(
	dispatcher: CoroutineDispatcher
) {
    val test = measureTimeMillis {
        coroutineScope {
            repeat(100) {
                launch(dispatcher) {
                    Thread.sleep(1000)
                }
            }
        }
    }
    println("$dispatcher took: $test")

}
```

![center|400](/image/real-resource-image/Pasted%20image%2020240203163338.png)

* IO Dispatcher에서 limitedParallelism을 사용하면 특정 작업을 위한 새로운 쓰레드 풀이 생성됨

* Default Dispatcher에서는 limitedParallelism사용시 기존 쓰레드풀 내에서 특정작업을 위한 쓰레드개수를 지정함

* 쓰레드에 대한 더 세밀한 조정을 위해 asCoroutineDispatcher함수로 dispatcher를 지정할 수 있음

```kotlin
val NUMBER_OF_THREADS = 20
val dispatcher = Executors
    .newFixedThreadPool(NUMBER_OF_THREADS)
    .asCoroutineDispatcher()
```

* limitedParallelism(1)로 동시성 제어를 할 수 있음

* VirtualThread를 위한 지원도 있음

```kotlin
val LoomDispatcher = Executors
    .newVirtualThreadPerTaskExecutor()
    .asCoroutineDispatcher()
```

## Unconfined Dispatcher

* 다른 Dispatcher들과 다르게 쓰레드를 변경하지 않음
* Unconfined Dispatcher를 사용하면, suspend를 만날때까지 이를 실행한 쓰레드에서 작업이 시작됨
* suspend 후 resume시에는 resume을 실행한 쓰레드에서 작업을 이어함
* 성능 측면에서는 쓰레드를 변경시키지 않아 유용할 수 있지만, main쓰레드를 중단할 수 있기에 추천되지 않음
* 일반적인 상황에서 유용하지 않음
* Unit Test시 사용

## Main dispatcher

* UI를 다루는 어플리케이션에서 사용하는거
  * ex) Android, JavaFx

https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-dispatcher/\
https://kt.academy/article/cc-dispatchers\
https://medium.com/@wind.orca.pe/dispatchers-kotlin-coroutines-659a5681f329\
https://kotlinlang.org/docs/coroutine-context-and-dispatchers.html#dispatchers-and-threads
