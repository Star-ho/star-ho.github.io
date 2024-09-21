---
date: 2024-05-03T23:37:49
updatedAt: 2024-09-22 00:01:07
tags:
  - JVM
  - Concurrency
  - hugo_blog
categories:
  - JVM
title: 2-Semaphore
lastmod: 2024-09-21T15:01:24.737Z
---
* 허가증을 유지함으로서 동시성을 제어

* acquire()메서드는 허가증이 사용가능할때까지 block하고 사용가능할때 허가증을 가짐

* release는 허가증을 추가하고, 잠재적으로 blocking되어있는 acquirer를 해제함

* 실제로 퍼미션 객체는 사용되지 않으며, 세마포어는 사용가능한 개수를 카운팅할 뿐임

* Semaphore는 자원에 대해 접근할수 있는 쓰레드의 수를 제한하는데 사용함

```java
 class Pool {
   private static final int MAX_AVAILABLE = 100;
   private final Semaphore available = new Semaphore(MAX_AVAILABLE, true);

   public Object getItem() throws InterruptedException {
     available.acquire();
     return getNextAvailableItem();
   }

   public void putItem(Object x) {
     if (markAsUnused(x))
       available.release();
   }

   // Not a particularly efficient data structure; just for demo

   protected Object[] items = ... whatever kinds of items being managed
   protected boolean[] used = new boolean[MAX_AVAILABLE];

   protected synchronized Object getNextAvailableItem() {
     for (int i = 0; i < MAX_AVAILABLE; ++i) {
       if (!used[i]) {
          used[i] = true;
          return items[i];
       }
     }
     return null; // not reached
   }

   protected synchronized boolean markAsUnused(Object item) {
     for (int i = 0; i < MAX_AVAILABLE; ++i) {
       if (item == items[i]) {
          if (used[i]) {
            used[i] = false;
            return true;
          } else
            return false;
       }
     }
     return false;
   }
 }

```

* ReentrantLock과 유사하게 lock을 얻어 자원을 확보한 이후에 작업을 진행함
* 허가증을 1개만 사용하여 metex처럼 사용 가능
* reentrantLock과 같이 공정성 여부를 파라미터로 받아 공정성 여부를 결정함

## vs ReentrantLock

* ReentrantLock은 1개의 자원에 대해 1개의 쓰레드만 접근이 가능함
* Semaphore는 1개의 자원에 대해 n개의 쓰레드 접근이 가능함
* ReentrantLock은 하나의 자원에 대해 여러번 시도하여 재진입이 가능함
  * 재진입한 횟수만큼 자원 해제를 해야함
  * Semaphore에서는 지원하지 않음
