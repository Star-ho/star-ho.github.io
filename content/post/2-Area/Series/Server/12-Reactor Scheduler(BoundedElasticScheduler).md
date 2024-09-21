---
date: 2024-03-05T22:11:03
updatedAt: 2024-09-21 23:54:48
tags:
  - Server-History
  - hugo_blog
  - Reactive
  - Server
categories:
  - Server-History
title: 12-Reactor Scheduler(BoundedElasticScheduler)
lastmod: 2024-09-21T14:54:48.003Z
---
## 서론

Reactor에 대해 여러가지 공부해 보았는데, reactor Scheduler에 대한 글이 없어 소스코드를 보며 분석하였다.\
reactor-netty에서 디폴트로 제공하는  BoundedElasticScheduler에서 어떻게 쓰레드에 작업이 할당되는지 알아보자

## Reactor Scheduler

실제 작업이 실행될 쓰레드를 할당하는 인터페이스이다.\
java reactor에서는 제공하는 여러가지 스케줄러를 제공하는데, BoundedElasticScheduler는 Scheduler의 구현체이다

## Schedulers

subscribeOn, publishOn에는 Schedulers의 정적 메서드를 사용하여 스케줄러를 지정하기에 Schedulers클래스부터 알아보자

필드에 대한 설명이다

### DEFAULT\_POOL\_SIZE

* 기본 풀 사이즈로, ParallelScheduler 사용 시쓰레드 수를 지정하는 필드이다.
* `reactor.schedulers.defaultPoolSize` 설정으로 값을 지정할 수 있으며 디폴트 값은 시스템의 CPU개수이다

### DEFAULT\_BOUNDED\_ELASTIC\_SIZE

* BoundedElasticScheduler에서 사용하는 쓰레드 풀 사이즈를 지정하는 필드이다.
* `reactor.schedulers.defaultBoundedElasticSize`로 설정할 수 있으며 디폴트는 시스템의 CPU개수 \* 10개이다

### DEFAULT\_BOUNDED\_ELASTIC\_QUEUESIZE

* BoundedElasticScheduler에서 쓰레드 별 큐사이즈를 지정하는 필드이다
* `reactor.schedulers.defaultBoundedElasticQueueSize`로 설정할 수 있으며 디폴트는 100,000개이다

### DEFAULT\_BOUNDED\_ELASTIC\_ON\_VIRTUAL\_THREADS

* BoundedElasticScheduler사용 시 가상쓰레드 여부를 결정하는 필드이다
* reactor.schedulers.defaultBoundedElasticOnVirtualThreads로 설정할 수있으며, 디폴트로 false이다

이제 스케줄러를 생성하는 메서드에 대해 알아보자

```java
public static Scheduler boundedElastic() {  
    return cache(CACHED_BOUNDED_ELASTIC, BOUNDED_ELASTIC, BOUNDED_ELASTIC_SUPPLIER);  
}
```

스케줄러를 생성하는 메서드는 static메서드이고 내부에서 cache로 관리한다.

* 한번 스케줄러를 생성하면 내부에서 캐싱된 스케줄러를 가져온다는것을 알 수있다.
* ImmediateScheduler, BoundedElasticScheduler, ParallelScheduler는 다 아래와 같은 형태이다

> prefix로 new가 붙은 메서드를 사용하거나 fromExecuter를 사용해서 새로운 스케줄러를 생성할 수 있다.

```java
static CachedScheduler cache(AtomicReference<CachedScheduler> reference, String key, Supplier<Scheduler> supplier) {  
    CachedScheduler s = reference.get();  
    if (s != null) {  
       return s;  
    }  
    s = new CachedScheduler(key, supplier.get());  
    if (reference.compareAndSet(null, s)) {  
       return s;  
    }  
    return reference.get();  
}
```

cache메서드 간단하다.\
캐싱된 스케줄러가 있는지 확인하고, 있으면 캐싱된 스케줄러를 반환하고, 없다면 인자로 받은 Scheduler Supplier로 스케줄러를 생성하고, 이를 캐싱한다.

그럼 이제 BoundedElasticScheduler를 생성하는 BoundedElasticSchedulerSupplier에 대해 알아보자

```java
class BoundedElasticSchedulerSupplier implements Supplier<Scheduler> {

	@Override
	public Scheduler get() {
		...
		return newBoundedElastic(DEFAULT_BOUNDED_ELASTIC_SIZE,
				DEFAULT_BOUNDED_ELASTIC_QUEUESIZE,
				BOUNDED_ELASTIC,
				BoundedElasticScheduler.DEFAULT_TTL_SECONDS,
				true);
	}
}
```

newBoundedElastic메서드에 Schedulers의 BoundedElasticScheduler의 설정값이 들어가는 것을 볼 수 있다.

## BoundedElasticScheduler

이제 BoundedElasticScheduler에서 작업을 할당하는 schedule메서드를 알아보자

```java
@Override  
public Disposable schedule(Runnable task, long delay, TimeUnit unit) {  
    //tasks running once will call dispose on the BoundedState, decreasing its usage by one  
    final BoundedState picked = state.currentResource.pick();  
    try {  
       return Schedulers.directSchedule(picked.executor, task, picked, delay, unit);  
    } catch (RejectedExecutionException ex) {  
       // ensure to free the BoundedState so it can be reused  
       picked.dispose();  
       throw ex;  
    }
```

로직은 간단한데, schedule메서드는 currentResource의 pick메서드를 호출하여 BoundedState를 가져오고, 스케줄러에 작업을 예약한다.

```java
BoundedState pick() {  
    for (; ; ) {  
       if (busyStates == ALL_SHUTDOWN) {
          return CREATING;
       }  
  
       int a = get(); 
       if (!idleQueue.isEmpty()) {  
          BoundedState bs = idleQueue.pollLast();  
          if (bs != null && bs.markPicked()) {  
             boolean accepted = setBusy(bs);  
             if (!accepted) { // shutdown in the meantime  
                bs.shutdown(true);  
                return CREATING;  
             }  
             return bs;  
          }  
       }  
       else if (a < parent.maxThreads) {  
          if (compareAndSet(a, a + 1)) {  
             ScheduledExecutorService s = Schedulers.decorateExecutorService(parent,  
                parent.createBoundedExecutorService());  
             if (newState.markPicked()) {  
                boolean accepted = setBusy(newState);  
                if (!accepted) { // shutdown in the meantime  
                   newState.shutdown(true);  
                   return CREATING;  
                }  
                return newState;  
             }  
          }  

       } else {  
          BoundedState s = choseOneBusy();  
          if (s != null && s.markPicked()) {  
             return s;  
          }  
       }  
    }  
}
```

pick메서드는 무한 루프를 돌며 사용 가능한 BoundedState를 가져온다

line 3에서 busyState가 전부 종료되어 있다면 종료상태의 BoundedState를 리턴한다

* CREATE enum으로 되어있지만, CREATE는 기본 실행기로서 종료와 같은 상태이므로 종료된 BoundedState를 리턴함
* 종료된 상태의 executer가 전달되기에 작업은 실행되지 않음

line 8에서는 idle한 BoundedState가 있는지 확인하고, 있다면 idleQueue에서 BoundedState를 하나 꺼내 리턴함

line 19에서는 현재 생성된 쓰레드 수가 최대 생성 가능한 쓰레드보다 작은지 확인 한다. 작다면 새로운 스케줄러 스레드를 생성해 BoundedState를 만들어  리턴함

line 33에서는 busy상태에 있는 BoundedState 중 작업이 가장 적게 할당된 BoundedState를 가져와 리턴한다.

다시 schedule메서드를 보자

```java
@Override  
public Disposable schedule(Runnable task, long delay, TimeUnit unit) {  
    //tasks running once will call dispose on the BoundedState, decreasing its usage by one  
    final BoundedState picked = state.currentResource.pick();  
    try {  
       return Schedulers.directSchedule(picked.executor, task, picked, delay, unit);  
    } catch (RejectedExecutionException ex) {  
       // ensure to free the BoundedState so it can be reused  
       picked.dispose();  
       throw ex;  
    }
}
```

pick으로 가져온 BoundedState의 executer를 directSchdule의 인자로 넘긴다

```java
static Disposable directSchedule(ScheduledExecutorService exec,  
       Runnable task,  
       @Nullable Disposable parent,  
       long delay,  
       TimeUnit unit) {  
    task = onSchedule(task);  
    SchedulerTask sr = new SchedulerTask(task, parent);  
    Future<?> f;  
    if (delay <= 0L) {  
       f = exec.submit((Callable<?>) sr);  
    }  
    else {  
       f = exec.schedule((Callable<?>) sr, delay, unit);  
    }  
    sr.setFuture(f);  
  
    return sr;  
}
```

directSchedule메서드를 살펴보자\
10번, 13번 라인에서 executer에게 작업을 할당한다\
작업을 할당하는 메서드를 살펴보자

```java
@Override  
public synchronized <T> Future<T> submit(Callable<T> task) {  
    ensureQueueCapacity(1);  
    return super.submit(task);  
}
```

synchronized 메서드이다

* synchronized 메서드이므로 객체마다 lock이 걸리고, 해당 메서드를 가지고있는 BoundedScheduledExecutorService는 static클래스이므로 해당 메서드는 하나의 프로세스에서 동시에 실행될 수 없다.

ensureQueueCapacity를 살펴보자

```java
void ensureQueueCapacity(int taskCount) {  
    if (queueCapacity == Integer.MAX_VALUE) {  
       return;  
    }  

    int queueSize = super.getQueue().size();  

    if ((queueSize + taskCount) > queueCapacity) {  
       throw Exceptions.failWithRejected(  
          "Task capacity of bounded elastic scheduler reached while scheduling " + taskCount + " tasks (" + (  
             queueSize + taskCount) + "/" + queueCapacity + ")");  
    }  
}
```

위 메서드는 현재 큐의 크기와, 추가된 작업의 개수의 합이 DEFAULT\_BOUNDED\_ELASTIC\_QUEUESIZE를 넘기는지 확인하고, 넘으면 에러를 리턴하는 로직이다.

***

pick메서드에서 최대 스레드 생성 개수, ensureQueueCapacity메서드에서 쓰레드 당 최대 작업개수를 검증하는 것을 확인할 수 있었다.\
pick메서드에서는 compareAndSet으로 동시성을 제어한다.\
ensureQueueCapacity메서드에서는 호출하는 메서드가 syncronized 메서드로 동시성을 제어한다.

## 추가 - BoundedElasticScheduler의 inner class

BoundedElasticScheduler 내부에 4개의 클래스가 있다.\
내부 클래스들을 알게 되면 BoundedElasticScheduler의 동작과정을 더 이해할 수 있다.

먼저 BoundedElasticScheduler의 선언부를 보자

```java
final class BoundedElasticScheduler implements Scheduler,  
    SchedulerState.DisposeAwaiter<BoundedElasticScheduler.BoundedServices>,  
    Scannable {  
...

    volatile SchedulerState<BoundedServices> state;  
    @SuppressWarnings("rawtypes")  
    static final AtomicReferenceFieldUpdater<BoundedElasticScheduler, SchedulerState> STATE =  
       AtomicReferenceFieldUpdater.newUpdater(BoundedElasticScheduler.class, SchedulerState.class, "state");
```

BoundedElasticScheduler는 Scheduler, SchedulerState.DisposeAwaiter, Scannable의 구현체이다.\
필드로 SchedulerState\<BoundedServices> 타입의 필드가 있는데, BoundedServices로 스케줄러의 상태를 관리한다.

```java
static final class BoundedServices extends AtomicInteger{
...
	final BoundedElasticScheduler parent;
	final Deque<BoundedState> idleQueue;
	volatile BusyStates busyStates;
...

BoundedState pick() {  
...


}
```

선언부를 보면, BoundedServices는 AtomicInteger를 상속받은 클래스이다.\
BoundedServices의 Integer 값은, 현재 실행되고 있는 쓰레드의 개수를 의미한다.

parent필드에서는 부모인 BoundedElasticScheduler을 가지고 있다\
idleQueue는 idle 상태인 BoundedState를 가지고 있다.\
busyStates는 BusyStates타입인데, busy상태인 BoundedState을 가지고 있다.\
pick메서드는 앞서 설명한 바와 같이, 작업을 실행할 BoundedState을 결정한다.

```java
static final class BusyStates {  
    final BoundedState[] array;  
    final boolean shutdown;  
  
    public BusyStates(BoundedState[] array, boolean shutdown) {  
       this.array = array;  
       this.shutdown = shutdown;  
    }  
}
```

BusyStates에서는 array 필드에 busy상태인 BoundedState을 가지고 있다.

```java
static class BoundedState implements Disposable, Scannable { 
  
    final BoundedServices parent;  
    final ScheduledExecutorService executor;  
  
    volatile int markCount;  
  
    BoundedState(BoundedServices parent, ScheduledExecutorService executor) {  
       this.parent = parent;  
       this.executor = executor;  
    }
}
```

parent필드는 부모인 BoundedService를 가지고 있다.\
executer는 ScheduledExecutorService을 가지고 있는데, 실제 생성자로 들어오는 클래스는 ScheduledExecutorService가 아닌, BoundedScheduledExecutorService을 가진다.\
markCount는 executer가 실행하고 있는 작업의 개수를 나타낸다.

```java
static final class BoundedScheduledExecutorService extends ScheduledThreadPoolExecutor  
    implements Scannable{
	
	final int queueCapacity;

	void ensureQueueCapacity(int taskCount) {  
	    if (queueCapacity == Integer.MAX_VALUE) {  
	       return;  
	    }  

	    int queueSize = super.getQueue().size();  

	    if ((queueSize + taskCount) > queueCapacity) {  
	       throw Exceptions.failWithRejected(  
	          "Task capacity of bounded elastic scheduler reached while scheduling " + taskCount + " tasks (" + (  
	             queueSize + taskCount) + "/" + queueCapacity + ")");  
	    }  
	}
	
	@Override  
	public synchronized ScheduledFuture<?> schedule(  
	    Runnable command,  
	    long delay,  
	    TimeUnit unit) {  
	    ensureQueueCapacity(1);  
	    return super.schedule(command, delay, unit);  
	}
}
```

BoundedScheduledExecutorServices는 ScheduledThreadPoolExecutor구현체이다\
ScheduledThreadPoolExecutor와 큰 차이라면 queueCapacity필드와 ensureQueueCapacity메서드를 가지고 있다는 것이다.\
schedule을 할때마다 현재 큐사이즈+새 태스크 개수를 확인해서 queueCapacity를 넘으면 에러를 발생시킨다.
