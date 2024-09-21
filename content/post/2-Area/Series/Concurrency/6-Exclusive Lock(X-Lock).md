---
date: 2024-03-02T22:41:01
updatedAt: 2024-09-21 23:55:29
tags:
  - hugo_blog
  - Concurrency
  - Concept
  - Database
categories:
  - Concurrency
title: 6-Exclusive Lock(X-Lock)
lastmod: 2024-09-21T14:55:33.953Z
---
1- X-Lock이 걸린 객체에 대해 다른 트랜잭션에서 읽기, 쓰기 불가능

* X-Lock이 걸린 객체에 대해 다른 객체에서 S-Lock, X-Lock 걸수 없음

* 변경 또는 삭제를 위해 락을 걸떄 활용

* Mysql의 Repeatable-Read는 Constent Read 기술을 사용해여 X-Lock걸린 row를 읽도록 함으로써 효율을 높임
  * X-Lock이 걸리기 전의 값을 읽음

https://dev.mysql.com/doc/refman/8.0/en/InnoDB-locking.html
