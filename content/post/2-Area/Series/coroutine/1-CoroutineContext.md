---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8590
tags:
  - Coroutine
  - hugo_blog
title: 1-CoroutineContext
lastmod: 2024-04-21T09:36:36.357Z
---
## 정의

*Persistent context for the coroutine. It is an indexed set of Element instances. An indexed set is a mix between a set and a map. Every element in this set has a unique Key*\*

* 코루틴의 영속 컨텍스트
* Element 인스턴스의 색인된 set이고, 색인된 set은 set과 map을 합친것,
* set안에 있는 각각의 element는 unique Key를 가짐

## CoroutineContext가 쓰이는곳

* coroutine builder의 첫번째 파라미터

```java
public fun CoroutineScope.launch(
   context: CoroutineContext = EmptyCoroutineContext,
   start: CoroutineStart = CoroutineStart.DEFAULT,
   block: suspend CoroutineScope.() -> Unit
): Job {
   ...
}
```

* Coroutine Scope의 인자

```java
public interface CoroutineScope {
    public val coroutineContext: CoroutineContext
}
```

* Continuation의 인자

```java
public interface Continuation<in T> {
    public val context: CoroutineContext
    public fun resumeWith(result: Result<T>)
}
```

* **코루틴의 대부분의 곳에 쓰이므로 매우 중요한 요소임!**

## 설명

```java
public interface CoroutineContext {  
	public operator fun <E : Element> get(key: Key<E>): E?
	
	public fun <R> fold(initial: R, operation: (R, Element) -> R): R  
	
	public operator fun plus(context: CoroutineContext): CoroutineContext{
		...
	}
	public fun minusKey(key: Key<*>): CoroutineContext
    
    public interface Key<E : Element>  

	public interface Element : CoroutineContext {  
		public val key: Key<*>  
  
        public override operator fun <E : Element> get(key: Key<E>): E? =  
            @Suppress("UNCHECKED_CAST")  
            if (this.key == key) this as E else null  
  
        public override fun <R> fold(initial: R, operation: (R, Element) -> R): R =  operation(initial, this)  
  
        public override fun minusKey(key: Key<*>): CoroutineContext =  
            if (this.key == key) EmptyCoroutineContext else this  
    }  
}
```

* 위의 코드는 CoroutineContext의 선언부를 간략화 한 것임

* CoroutineContext 내부에는 get, fold, plus, minusKey메서드와 Key, Element의 선언부가 잇음

* CoroutineContext는 minus, plus, fold와 같은 연산이 가능
  * plus시에는 두개의 CoroutineContext내부의 Key가 합쳐짐
  * 두개의 CoroutineContext가 같은 Key를 가지고 있다면 더하는 쪽의 키가 우선임
    * 같은 키를 가지고 있을때 더하면 항상 새로운 키가 저장됨
  * 두개의 CoroutineContext합치면, CombinedContext가 됨
    * CombinedContext는 CoroutineContext의 구현 중 하나임
    * CombinedContext는 내부에 CoroutineContext타입의 left필드가 존재
    * CombinedContext에서 getKey를 호출하면, left를 반복호출하여 해당하는 키가 있는지 찾고 없으면 null 리턴
    * CoroutineContext가 여러 Key를 가지고 있을 수 있는 이유임

* Element는 Context를 상속받는 요소
  * Element의 구현으로는 `Job`, `CoroutineName`, `CouroutineDispatcher`등 여러가지가 존재
  * 각 Element는 Key로 식별이 가능하며, 각 Key는 참조로 비교됨
    * 하위 context에서 같은키를 가지고 있다면, 하위 context의 키가 우선함

참고자료\
https://kt.academy/article/cc-coroutine-context\
https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/-coroutine-context/\
https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-global-scope/coroutine-context.html\
https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/-coroutine-context/-element/\
https://github.com/JetBrains/kotlin/blob/0938b46726b9c6938df309098316ce741815bb55/libraries/stdlib/src/kotlin/coroutines/CoroutineContext.kt#L59
