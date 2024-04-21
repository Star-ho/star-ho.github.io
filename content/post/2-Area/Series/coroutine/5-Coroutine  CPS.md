---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8540
tags:
  - hugo_blog
  - Coroutine
categories: Coroutine
title: 5-Coroutine  CPS
lastmod: 2024-04-21T09:36:36.349Z
---
* Kotlin Coroutine은 일시중단을 구현하기 위해 ContinuosPassing style을 적용하였음

## CPS스타일로 변환된 Suspend 함수

* 실제 코드가 아닌, 중요한 로직만 정리함

```kotlin
suspend fun printUser(token: String) {
  println("Before")
  val userId = getUserId(token) // suspending
  println("Got userId: $userId")
  val userName = getUserName(userId, token) // suspending
  println(User(userId, userName))
  println("After")
}
```

```kotlin
fun printUser(
    token: String,
    continuation: Continuation<*>
        ): Any {
    val continuation = continuation as? PrintUserContinuation
    ?: PrintUserContinuation(
        continuation as Continuation<Unit>,
        token
    )

    var result: Result<Any>? = continuation.result
    var userId: String? = continuation.userId
    val userName: String

    if (continuation.label == 0) {
        println("Before")
        continuation.label = 1
        val res = getUserId(token, continuation)
        if (res == COROUTINE_SUSPENDED) {
            return COROUTINE_SUSPENDED
        }
        result = Result.success(res)
    }
    if (continuation.label == 1) {
        userId = result!!.getOrThrow() as String
        println("Got userId: $userId")
        continuation.label = 2
        continuation.userId = userId
        val res = getUserName(userId, continuation)
        if (res == COROUTINE_SUSPENDED) {
            return COROUTINE_SUSPENDED
        }
        result = Result.success(res)
    }
    if (continuation.label == 2) {
        userName = result!!.getOrThrow() as String
        println(User(userId as String, userName))
        println("After")
        return Unit
    }
    error("Impossible")
}

class PrintUserContinuation(
    val completion: Continuation<Unit>,
    val token: String
) : Continuation<String> {
    override val context: CoroutineContext
    get() = completion.context

    var label = 0
    var result: Result<Any>? = null
    var userId: String? = null

    override fun resumeWith(result: Result<String>) {
        this.result = result
        val res = try {
            val r = printUser(token, this)
            if (r == COROUTINE_SUSPENDED) return
            Result.success(r as Unit)
        } catch (e: Throwable) {
            Result.failure(e)
        }
        completion.resumeWith(res)
    }
}
```

* 함수의 오퍼레이션이 변경됨
  * 마지막 인자로 continuation이 생김
    * continuation은 현재 코루틴의 상태를 가지고 있는 상태머신임
    * 항상 function의 마지막 인자로 추가됨
  * return 타입이 Any로 변경됨
    * Any?로 바뀌는 이유는 실제 리턴타입 뿐만아니라, suspend된다면 COROUTINE\_SUSPENDED을 반환해야하기 때문

> 추후 kotlin에 유니온 타입이 추가된다면 User?|COROUTINE\_SUSPENDED가 될 수 있음

* 5번 라인, Continuation이 해당 함수의 Continuation인지 확인하고, 아니라면 생성
  * resume될떄는 해당 함수의 Continuation이므로, 처음 실행될때만 생성함

* 11,12,13번 라인, 지역변수들을 선언하고, 값을 대입
  * 11번 result변수는 직전에 호출한 suspend 함수의 결과 가짐
  * 12번 userId는 여러 단계(1,2)에 걸쳐서 필요하므로 Continuation에 저장됨
  * 13번 userName은 한번의 단계에서만 사용하므로 result로 가져올수 있어 따로 저장되지 않음

* Continuation은 label을 가짐
  * label로 현재 어디까지 코드가 진행되었는지 파악하고, 다음 실행때 어디부터 시작할지 결정함

* suspend된다면, COROUTINE\_SUSPENDED을 리턴 후 중단이 끝난 후 다시시작함
  * io작업이 발생한다면 작업을 끝내지 못하므로 우선 COROUTINE\_SUSPENDED을 리턴함

* suspend이후 resume된다면, PrintUserContinuation의 resumeWith가 호출됨
  * 22번 라인과 56번라인이 동일 기능을 함
  * 앞서 말한다로 직전에 호출한 suspend 함수의 결과를 result변수에 넣음

* 콜스택 마지막에 있는 함수의 continuation이 supend후 resume되고, 작업을 다 끝마치면, 바로 상위 함수의 continuation의 resume을 호출함\
  ![center|600](/image/real-resource-image/Pasted%20image%2020240204111912.png)

https://kt.academy/article/cc-under-the-hood
