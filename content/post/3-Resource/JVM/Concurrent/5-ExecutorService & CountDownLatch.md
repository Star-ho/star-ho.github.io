---
date: 2024-04-05T17:25:00
updatedAt: 2024-09-22 16:46:42
tags:
  - JVM
  - Concurrency
  - hugo_blog
categories:
  - JVM
title: 5-ExecutorService & CountDownLatch
lastmod: 2024-09-22T07:46:42.178Z
---
# ExecutorService

* 비동기 작업을 할때 쓰레드관리가 복잡한 과정임
  * ExecutorService가 복잡한 쓰레드관리를 단순화 시켜줌
* 하나이상의 비동기 작업을 과정을 추적하기 위한 Future를 생성하는 메서드와 종료관리 메서드를 제공
* Excutor를 상속받았기에 execute메서드와 ExecutorService자체에서 제공하는 submit 메서드, shutdown, shutdownNow, awaitTermination등의 메서드가 있음

## Method

### execute(Runnable command)

* Runnable한 인자를 받아 미래에 실행시킴
* void를 리턴함

### submit(Runnable command)

* execute와 마찬가지고러 Runnable한 인자와 Callable한 인자를 받아 미래에 실행시킴
* 인자의 수행 결과를 Future로 감싸서 리턴함

### awaitTermination(long timeout, TimeUnit unit)

* 시간을 인자로 받으며, 모든 작업이 끝나거나, 시간 초과되거나, 인터럽트가 발생할때까지 쓰레드를 block시킴

### shutdown()

* 이전에 제출된 작업은 유지하지만, 새로운 작업은 받지않음
* 제출된 작업이 완료되면 종료함

### shutdownNow()

* 실행중인 작업과 대기중인 작업 모두 중지하고, 실행 대기중인 작업을 반환함

# CountDownlatch

* 하나 이상의 스레드가 다른 쓰레드의 작업이 완료될때 까지 기다려주는 보조 동기화 장치
* 생성자로 count를 받으며, count가 0이 되면 await를 즉시 리턴함

## 메서드

### CountDownLatch(int count)

* 생성자로 count를 받고, await를 호출하면 해당 count가 0이 될때까지 스레드를 wait함

### countDown()

* count를 1감소시킴, 0이되면 await중인 스레드의 release함

### getCount()

* 현재 count를 가져옴

### await()

* 스레드가 인터럽트 되지 않는 한, count가 0이 될때까지 현재 쓰레드를 wait함

https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CountDownLatch.html\
https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ExecutorService.html
