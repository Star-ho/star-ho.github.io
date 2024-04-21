---
date: 2024-02-19T13:24:16
updatedAt: 2024-04-21 18:34:36+3280
tags:
  - JVM
  - Garbage-Collection
  - hugo_blog
categories:
  - JVM
title: 0-Garbage Collection Concept
lastmod: 2024-04-21T09:36:38.171Z
---
* 메모리 관리 기법중 하나로 프로그램이 동적으로 할당했던 메모리 영역 중에서 필요없게된 영역을 해제하는 기능

## Garbage Collection 과정

### Step 1. Marking

* Garbage Collector가 메모리 조각중에서 사용되고 있는 것과 사용되지 않는것을 찾아 marking하는 단계\
  ![center|400](/image/real-resource-image/Pasted%20image%2020240222132054.png)
* 그림에서 참조된 객체는 blue, 참조되지 않은 객체는 주황색임
* marking단계에서는 삭제를 하기 위한 객체를 찾는 과정
* 시스템을 모두 스캔해야 하는 경우 시간이 많이 소요될 수 있음

### Step 2. Normal Deletion

* Step 1에서 찾은 객체를 삭제하는 단계\
  ![center|400](/image/real-resource-image/Pasted%20image%2020240222132435.png)
* memory allocator는 새 객체를 할당 할 수 있는 여유 공간 블록에 대한 참조를 보유
  * memory allocator는 비어있는 공간에 대한 참조를 가지고, 할당이 필요한 비어있는 공간을 검색

### Step 2a. Deletion with Compacting

* 추가적인 성능 향상을 위해, 참조되지 않는 객체를 삭제하면서 남아있는 참조 객체를 압축할 수 있음
* 참조된 객체를 함께 이동함으로써, 메모리 할당은 더 빠르고 쉬워짐\
  ![center|400](/image/real-resource-image/Pasted%20image%2020240222133340.png)
* Memory Allocator는 비어있는 공간에 대한 첫번째 참조를 가지고, 메모리를 순차적으로 할당

## Generation Garbage Collection

![center|600](/image/real-resource-image/Pasted%20image%2020240222224100.png)

* JVM의 모든 객체를 marking하고 compact하는것은 비효율적임
* 시간이 지날수록 객체의 숫자는 늘어가며 이에따라 garbage collection의 시간은 증가할것임
* 하지만 대부분의 객체의 수명은 짧기에 Generation Garbage Collection을 사용함
  * 경험적으로 증명됨
* 위의 그래프에서 알 수 있듯이 대부분의 객체의 수명은 짧고, 시간이 지남에 따라 객체의 숫자가 줄어든다는 것을 파악할 수 있음

![center|600](/image/real-resource-image/Pasted%20image%2020240222224505.png)

* JVM heap은 위와 같이 3가지 구역(Young, Old, Permanent)으로 나뉨
* Yong Generation
  * 새로운 객체가 할당되고, aged되는 곳
  * Yong Generation이 가득차면 minor gc가 발생함
  * minor gc는 객체의 사망률이 높을때 최적화됨
  * dead object는 빠르게 수거됨
  * 살아남은 일부 객체는 Old Generation으로 이동함
  * minor gc가 발생할때 Stop-the-world가 발생함

> Stop-the-world가 발생하면 모든 어플리케이션의 쓰레드가 중지됨

* Old Generation
  * 오래 살아남은 객체가 저장되는곳
  * Yong Generation에는 임계값이 존재하고 임계값을 넘어서 생존하는 경우 Old Generation으로 이동함
  * 언젠간 Old Generation에도 Garbage Collection이 발생해야 하고 이것을 major GC로 명명함
  * major gc에서도 Stop-the-world가 발생함
  * major gc는 모든 살아있는 객체를 대상으로 하기에 느림
  * 반응형 애플리케이션에서는 major gc가 최소한으로 발생해야함
  * major gc는 Old Generation에서 사용하는 garbage collector 종류에 따라 Stop-the-world의 시간이 결정됨

* Permanent Generation
  * JVM의 class와 Method를 describe하기 위한 메타데이터가 저장됨
  * Permanent Generation는 어플리케이션에서 사용중인 클래스를 기반으로 런타임에 JVM에 의해 채워짐
  * Java SE 라이브러리 클래스 및 메서드가 채워질 수 있음
  * JVM이 더 이상 필요없다고 판단하거나, 다른 클래스를 위한 공간이 필요할경우 unload될 수 있음
  * Permanent Generation은 full garbage collection에 포함됨

> full garbage collection이란?

https://d2.naver.com/helloworld/1329\
https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html
