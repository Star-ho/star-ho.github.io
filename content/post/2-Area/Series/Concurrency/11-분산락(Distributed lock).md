---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8780
tags:
  - Concurrency
  - Concept
  - hugo_blog
categories: Concurrency
title: 11-분산락(Distributed lock)
lastmod: 2024-04-21T09:36:36.397Z
---
* named lock

* mysql, redis 등 named lock을 지원하는 저장소에서 사용가능

* redis서버가 없는 상황에서 mysql named lock도 유용한 선택지임

* mysql은 connection 끊기면 lock도 풀림

* **분산락은 자원에대한 락이 아닌 작업,행위에 대한 락**

* 하나의 작업이 한번만 실행되게 할때 유용함
  * 따닥 방지

* 데이터베이스에 부하가 가지않음
  * but, 실제로 작업하는 row가 db lock이 걸려있을시 처리

* 실제로 사용하기 위해 멱등성 보장이 필요할 수 있음
  * https://martin.kleppmann.com/2016/02/08/how-to-do-distributed-locking.html
