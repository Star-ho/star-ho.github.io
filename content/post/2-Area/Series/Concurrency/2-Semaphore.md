---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8750
tags:
  - Concurrency
  - hugo_blog
  - Concept
categories: Concurrency
title: 2-Semaphore
lastmod: 2024-04-21T09:36:36.390Z
---
* 임계영역을 보호하는 방법

* 여러 스레드의 임계영역에 대한 엑세스를 제어

* Mutex와의 차이점은 Mutex는 하나의 자원에 대한 엑세스를 제어하는 반면, Semaphore는 여러자원에 대한 엑세스를 제어

* 화장실이 3개이고 키도 3개일때

* 키가 하나도 없다면 화장실 이용불가

* 키가 1개 있다면 화장실 이용가능

* 키가 2개 있어도 화장실 이용가능

* but, 어느 화장실 칸이 비었는지 모르기에 추가적인 정보가 더 필요함

* [추가적인 정보가 궁금하다면 여기](https://barrgroup.com/embedded-systems/how-to/rtos-mutex-semaphore)

### 참고자료

[식사하는 철학자들 문제](https://ko.wikipedia.org/wiki/%EC%8B%9D%EC%82%AC%ED%95%98%EB%8A%94_%EC%B2%A0%ED%95%99%EC%9E%90%EB%93%A4_%EB%AC%B8%EC%A0%9C)\
https://stackoverflow.com/a/2332868/22483906\
https://stackoverflow.com/questions/2350544/in-what-situation-do-you-use-a-semaphore-over-a-mutex-in-c/2350628#2350628\
https://stackoverflow.com/questions/34519/what-is-a-semaphore/40238#40238

\#Concurrency
