---
date: 2024-02-22T23:19:00
updatedAt: 2024-04-21 18:36:38+1720
tags:
  - JVM
  - Garbage-Collection
  - hugo_blog
categories:
  - JVM
title: 2-Garbage Collection Types
lastmod: 2024-04-21T09:36:38.172Z
---
## Serial GC

* CPU 코어나 메모리가 적을 때 유용
* 하나의 서버에 여러 jvm이 실행되는 환경에서 유용
* major GC와 minor GC가 serially하게 적용됨
* mark-compact-swap 방식을 사용
* 오래된 메모리를 heap의 시작점에 두고, 새로 생성된 메모리를 heap의 마지막에 두어 새로 생성된 메모리가 연속적으로 할당되게함
* -XX:+UseSerialGC 로 사용가능

## Parallel GC

* 사용하는 알고리즘은 Serial GC와 같으나, 여러 스레드를 사용함

* CPU코어가 1개 이상일때 많을때 유용함

* CPU코어가 N개일때 N개의 garbage Collector를 사용
  * 옵션으로 garbage Collector개수 설정 가능

* CPU코어가 1개인 환경에서는 Parallel GC를 사용하더라도 해당 Serial GC가 사용됨

* ParallelGC는 2가지 가 있음

### ParallelGC

* Old영역은 싱글스레드, Young 영역은 멀티스레드로 동작
* Old영역의 compact도 싱글스레드로 동작
* `-XX:+UseParallelGC`로 사용가능

### ParallelOldGC

* Old영역, Young영역 둘다 멀티스레드로 동작
* compact도 멀티스레드로 동작
* `-XX:+UseParallelOldGC`로 사용가능

## The Concurrent Mark Sweep (CMS) Collector

* tenured영역을 collect하는 GC
* GC를 애플리케이션 스레드와 동시에 수행하여 애플리케이션의 일시중단 시간을 최소화 하려함
* live객체를 이동, 복사하거나 압축하지 않음
* 조각화가 문제가 되는경우 더 큰 힙을 할당해야함
* `-XX:+UseConcMarkSweepGC`로 사용가능
* *거의 사용되지 않음*

## G1 GC

![center|600](/image/real-resource-image/Pasted%20image%2020240304223056.png)

* 아래와 같이 바둑판 영역에 객체를 할당하고 GC를 실행함
* 해당 영역에 데이터가 꽉 차면 다른 영역에 객체를 할당하고 gc를 실행함

https://d2.naver.com/helloworld/1329\
https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html
