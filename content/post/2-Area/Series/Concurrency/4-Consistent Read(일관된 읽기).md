---
date: 2024-03-01T22:41:05
updatedAt: 2024-09-22 16:07:21
tags:
  - Concurrency
  - hugo_blog
  - Concept
categories:
  - Concurrency
  - Concurrent-series
title: 4-Consistent Read(일관된 읽기)
lastmod: 2024-09-22T07:07:21.425Z
---
* 첫번째 읽을때 스냅샷을 생성함으로써, 다른 트랜잭션에서 발생한 변경과는 무관하게, 스냅샷을 생성했을 때의 데이터를 읽을수 있는 것을 말함

* 다른 트랜잭션에서 데이터를 변경해도, undo log에 기록되어 변경전 데이터를 읽을 수 있음

* 이로인해 동시성 문제를 해결
  * undo log를 사용하지 않는다면, 어떤 row를 읽은 트랜잭션이 있다면 해당 트랜잭션이 끝날때 까지 해당 row를 접근하지 못하게 해야 일관된 읽기를 달성할 수 있음
  * 하지만 undo log를 사용함으로 각각의 트랜잭션에서는 데이터를 자유롭게 변경해도, 변경전 데이터가 undo log에 있으므로, 변경된 데이터에 대해서는 undo log를 읽어와 일관된 읽기를 달성할 수 있음

* REPEATABLE READ와 READ COMMITTED에서 기본으로 사용됨

*  ALTER TABLE문과 DROP TABLE문에서는 작동하지 않음

### 참고자료

https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos\_consistent\_read\
https://dev.mysql.com/doc/refman/8.0/en/InnoDB-consistent-read.html
