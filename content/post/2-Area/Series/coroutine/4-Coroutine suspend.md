---
date: 2024-03-03T22:41:05
updatedAt: 2024-09-22 16:06:47
tags:
  - Coroutine
  - hugo_blog
  - JVM
categories:
  - Coroutine
  - Coroutine-series
title: 4-Coroutine suspend
lastmod: 2024-09-22T07:06:47.612Z
---
* coroutine의 suspend는 게임을 일시 중단하는 것과 유사함
  * 게임을 잠깐 중단했다가 resume하면 게임이 다시 시작됨
  * 이것은 suspend와 유사

```kotlin
suspend fun main() {
    println("Before")

    suspendCoroutine<Unit> { continuation ->
        continuation.resume(Unit)
    }

    println("After")
}
```

* suspend 만 있다면 중지되고 재시작되지 않음
* suspend 후 작업을 재개하려면 suspend 내에서 continuation을 resume을 호출해야함

```kotlin
private val executor =
    Executors.newSingleThreadScheduledExecutor {
        Thread(it, "scheduler").apply { isDaemon = true }
    }

suspend fun delay(timeMillis: Long): Unit =
    suspendCoroutine { cont ->
        executor.schedule({
            cont.resume(Unit)
        }, timeMillis, TimeUnit.MILLISECONDS)
    }

suspend fun main() {
    println("Before")

    delay(1000)

    println("After")
}
// Before
// (1 second delay)
// After
```

* 위 코드는 delay 함수의 간략한 구현임
* 로직을 지연 후  재실행할때 쓰레드를 새로 생성하지 않고 지연이 가능
* delay 함수는 타이머를 사용하여 설정된 시간이 지나면 ***resume***을 수행합니다.
* delay를 실행하는 하나의 쓰레드만 존재
  * 모든 delay를 하나의 스레드로 관리
  * delay를 100번해도 하나의 쓰레드로 관리되므로 쓰레드가 더 생성되지 않음

```kotlin
suspend fun requestUser(): User {
    return suspendCancellableCoroutine<User> { cont ->
        requestUser { resp ->
            if (resp.isSuccessful) {
                cont.resume(resp.data)
            } else {
                val e = ApiException(
                    resp.code,
                    resp.message
                )
                cont.resumeWithException(e)
            }
        }
    }
}

suspend fun requestNews(): News {
    return suspendCancellableCoroutine<News> { cont ->
        requestNews(
            onSuccess = { news -> cont.resume(news) },
            onError = { e -> cont.resumeWithException(e) }
        )
    }
}
```

* 위와같이 suspendCancellableCoroutine으로 성공시와 실패시 로직을 다르게 구현할 수 있음

```kotlin
// Do not do this
var continuation: Continuation<Unit>? = null

suspend fun suspendAndSetContinuation() {
    suspendCoroutine<Unit> { cont ->
        continuation = cont
    }
}

suspend fun main() {
    println("Before")

    suspendAndSetContinuation()
    continuation?.resume(Unit)

    println("After")
}
// Before
```

* suspend은 함수를 중지하는것이 아닌, coroutine을 중지하는 것임
* suspendAndSetContinuation에서 suspend후 바로 다음 라인(14번) continuation?.resume(Unit)이 실행되어 실행이 재개될 것 처럼 보이지만 재개되지 않음

https://kt.academy/article/cc-suspension
