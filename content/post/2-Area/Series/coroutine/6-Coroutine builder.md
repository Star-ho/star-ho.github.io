---
date: 2024-03-03T22:41:07
updatedAt: 2024-09-21 23:52:43
tags:
  - hugo_blog
  - Coroutine
  - JVM
categories:
  - Coroutine
title: 6-Coroutine builder
lastmod: 2024-09-21T14:53:49.345Z
---
* suspend함수는 Continuation을 다른 suspend함수에 전달해야 함
* 일반함수에서 suspend를 호출 할 수 없음
* 어디서 suspend함수를 호출해야 하는가?
* **Coroutine builder**
* 일반세계와 suspend세계를 연결하는 다리역할

## launch

* 개념적으로 새로운 쓰레드를 생성하는 것과 유사함
* 코루틴을 시작하고, 독립적으로 실행함

```kotlin
public fun CoroutineScope.launch(  
    context: CoroutineContext = EmptyCoroutineContext,  
    start: CoroutineStart = CoroutineStart.DEFAULT,  
    block: suspend CoroutineScope.() -> Unit  
): Job {  
    val newContext = newCoroutineContext(context)  
    val coroutine = if (start.isLazy)  
        LazyStandaloneCoroutine(newContext, block) else  
        StandaloneCoroutine(newContext, active = true)  
    coroutine.start(start, coroutine, block)  
    return coroutine  
}
```

* CoroutineScope의 extension function임
* coroutine context를 받아, 해당 context내에서 3번쨰 인자로 받은 block을 실행시킴

```kotlin
fun main() {  
    GlobalScope.launch {  
        delay(1000L)  
        println("World!")  
    }  
      
    GlobalScope.launch {  
        delay(1000L)  
        println("World!")  
    }
  
    println("Hello,")  
    Thread.sleep(1500L)  
}// Hello,  
// (1 sec)  
// World!  
// World!  
```

* main 함수에서 Thread.sleep()을 호출하지 않는다면, Hello만 출력됨
  * launch내의 delay는 실제로 쓰레드를 block하지 않음

## async

```kotlin
public fun <T> CoroutineScope.async(  
    context: CoroutineContext = EmptyCoroutineContext,  
    start: CoroutineStart = CoroutineStart.DEFAULT,  
    block: suspend CoroutineScope.() -> T  
): Deferred<T> {  
    val newContext = newCoroutineContext(context)  
    val coroutine = if (start.isLazy)  
        LazyDeferredCoroutine(newContext, block) else  
        DeferredCoroutine<T>(newContext, active = true)  
    coroutine.start(start, coroutine, block)  
    return coroutine  
}
```

* launch와 비슷하지만 async는 값을 Deferred로 감싸서 return함
* Deferred는 suspending method await를 가짐
  * 값이 준비되면 Deferred에 저장되고, await호출시 값을 리턴함
  * 값이 준비되기전 await호출 시, 값이 준비될 떄까지 suspend됨

## runBlocking

```kotlin
@Throws(InterruptedException::class)  
public actual fun <T> runBlocking(context: CoroutineContext, block: suspend CoroutineScope.() -> T): T {  
    contract {  
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)  
    }  
    val currentThread = Thread.currentThread()  
    val contextInterceptor = context[ContinuationInterceptor]  
    val eventLoop: EventLoop?  
    val newContext: CoroutineContext  
    if (contextInterceptor == null) {  
        // create or use private event loop if no dispatcher is specified  
        eventLoop = ThreadLocalEventLoop.eventLoop  
        newContext = GlobalScope.newCoroutineContext(context + eventLoop)  
    } else {  
        // See if context's interceptor is an event loop that we shall use (to support TestContext)  
        // or take an existing thread-local event loop if present to avoid blocking it (but don't create one)        
        eventLoop = (contextInterceptor as? EventLoop)?.takeIf { it.shouldBeProcessedFromContext() }  
            ?: ThreadLocalEventLoop.currentOrNull()  
        newContext = GlobalScope.newCoroutineContext(context)  
    }  
    val coroutine = BlockingCoroutine<T>(newContext, currentThread, eventLoop)  
    coroutine.start(CoroutineStart.DEFAULT, coroutine, block)  
    return coroutine.joinBlocking()  
}
```

* coroutine의 일반적인 규칙은 thread를 block하지 않는다 이지만, runblocking은 다른 coroutine builder와는 다르게 쓰레드를 block함
* main function과 같이, 쓰레드를 block하지마 않으면 프로세스가 종료되기 때문에 이러한 경우 runBlocking을 사용해야함
  * runBlocking은 새로운 코루틴을 실행하고, 현재 쓰레드를 코루틴이 완료될 때 까지 block함
* runBlocking인자로 Dispatcher를 전달하여 다른 쓰레드에서 runBlocking을 실행하게 할 수 있음
  * 다른 쓰레드에서 runBlocking을 실행해도 runBlocking을 실행한 쓰레드를 Block함
  * Dispatcher는 코루틴을 실행할 쓰레드를 선택하는 것으로, 현재 쓰레드를 block하는 것을 막을 수 없음
* CoroutineScope의 확장함수가 아님, CoroutineScope외부에서 사용 가능
  * 완료될때까지 쓰레드를 block하므로 CoroutineScope외부에서 사용하는건 권장하지 않음

## Structured Concurrency

* launch와 async함수는 CoroutineScope의 extenstion함수 임

* 따로 CoroutineScope를 지정하지 않는다면, 해당 함수를 실행한 CoroutineScope의 확장함수로 동작
  * 부모-자식관계 형성

* 부모자식 관계가 형성된다면 아래와 같은 관계가 됨
  * 자식 코루틴은 부모 코루틴으로부터 context를 상속 받음
  * 부모 코루틴은 자식 코루틴이 끝날때까지 suspend됨
  * 부모 코루틴이 cancel되면 자식 코루틴도 cancel됨
  * 자식 코루틴에서 에러 발생시 부모 코루틴에도 전파됨

* CoroutineScope를 따로 지정한다면 부모-자식 관계가 아니므로 launch와 async의 종료를 기다리지 않음

```kotlin
fun main(){  
    runBlocking {  
        println("start")  
        CoroutineScope(EmptyCoroutineContext).launch(CoroutineName("123")) {  
            delay(100)  
            println("another scope!")  
        }  
        println("end")  
    }  
}
//start  
//end
```

> suspend 함수 내부에서 launch, async를 호출할떄는 coroutineScope{}로 코루틴스코프를 새로 생성 후 호출해야함

## withContext

```kotlin
public suspend fun <T> withContext(  
    context: CoroutineContext,  
    block: suspend CoroutineScope.() -> T  
): T {  
    contract {  
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)  
    }  
    return suspendCoroutineUninterceptedOrReturn sc@ { uCont ->  
        // compute new context  
        val oldContext = uCont.context  
        // Copy CopyableThreadContextElement if necessary  
        val newContext = oldContext.newCoroutineContext(context)  
        // always check for cancellation of new context  
        newContext.ensureActive()  
        // FAST PATH #1 -- new context is the same as the old one  
        if (newContext === oldContext) {  
            val coroutine = ScopeCoroutine(newContext, uCont)  
            return@sc coroutine.startUndispatchedOrReturn(coroutine, block)  
        }  
        // FAST PATH #2 -- the new dispatcher is the same as the old one (something else changed)  
        // `equals` is used by design (see equals implementation is wrapper context like ExecutorCoroutineDispatcher)        
        if (newContext[ContinuationInterceptor] == oldContext[ContinuationInterceptor]) {  
            val coroutine = UndispatchedCoroutine(newContext, uCont)  
            // There are changes in the context, so this thread needs to be updated  
            withCoroutineContext(coroutine.context, null) {  
                return@sc coroutine.startUndispatchedOrReturn(coroutine, block)  
            }  
        }  
        // SLOW PATH -- use new dispatcher  
        val coroutine = DispatchedCoroutine(newContext, uCont)  
        block.startCoroutineCancellable(coroutine, coroutine)  
        coroutine.getResult()  
    }  
}
```

* 결과를 리턴한점에서 async와 많이 비교됨
* withContext는 block이 끝날때까지 현재 coroutine을 suspend함
* async와의 차이점
  * 인자로 CoroutineStart을 받지않는 것
    * 즉시 실행되므로 CoroutineStart가 필요없음
  * context의 디폴트 값이 없는 것
    * withContext는 현재 Context가 아닌 다른 Context로 실행할경우 사용하는것이기에, Context를 인자로 받아야함
  * CoroutineScope의 확장함수가 아니라는 것
    * CoroutineScope

## CoroutineStart

* 코루틴 빌더의 시작 옵션을 설정
* DEFAULT 
  * 해당 컨텍스트에 코루틴을 실행하도록 즉시 예약함
* LAZY
  * 해당 코루틴이 필요할때까지, 코루틴 시작을 늦춤
* ATOMIC 
  * 해당 컨텍스트에 실행할 코루틴을 원자적으로(취소할 수 없는 방식으로) 예약함
* UNDISPATCHED
  * 현재 쓰레드애 첫번째 suspension point를 만날때까지 즉시 코루틴을 실행함

https://medium.com/@wind.orca.pe/kotlin-coroutines-coroutine-builders-korean-recap-24a36300513b\
https://kotlinlang.org/docs/coroutines-basics.html#an-explicit-job\
https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-start/
