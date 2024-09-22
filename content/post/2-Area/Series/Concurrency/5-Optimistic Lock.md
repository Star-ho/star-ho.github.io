---
date: 2024-03-01T22:41:07
updatedAt: 2024-09-22 16:07:23
tags:
  - Concurrency
  - lock
  - hugo_blog
categories:
  - Concurrency
  - Concurrent-series
title: 5-Optimistic Lock
lastmod: 2024-09-22T07:07:23.232Z
---
* CAS(compare and Set)이라고도 함
* 데이터베이스 수준이 아닌 어플리케이션 수준에서 자원을 관리
* record에 version관련 컬럼을 추가하여 자원을 관리
* 변경이 발생할때마다 version값에 1을 추가
  * 0,1으로 하면 안됨
    * 변경이 두번 발생하면 0,1,0이 되버리기 때문에 오류가 발생함
* 장점
  * 데이터베이스에서 lock을 걸지 않기 때문에 pessimistic lock보다 데이터 베이스 부하가 적음

### 참고자료

https://en.wikipedia.org/wiki/Optimistic\_concurrency\_control
