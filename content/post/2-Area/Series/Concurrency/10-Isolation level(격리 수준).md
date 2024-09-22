---
date: 2024-03-02T22:41:32
updatedAt: 2024-09-22 15:58:32
tags:
  - Concurrency
  - Database
  - hugo_blog
  - Concept
categories:
  - Concurrency
title: 10-Isolation level(격리 수준)
lastmod: 2024-09-22T06:58:35.574Z
---
* 여러 트랜잭션이 동시에 변경을 수행하고 쿼리를 수행할 때 성능과 안정성, 일관성 및 결과 재현성 간의 균형을 미세 조정하는 설정

## Read Uncommitted

* 가장 낮은 격리 수준
* 커밋되지 않은 다른 트랜잭션의 변경 내용을 읽을 수 있음
* 어떤 트랜잭션의 변경 내용이 COMMIT이나 ROLLBACK과 상관없이 다른 트랜잭션에서 보여짐

## Read Committed

* 다른 트랜잭션에서 커밋되지 않은 데이터는 읽을수 없음
* Dirty Read(더티 리드)문제는 해결되지만, Phantom Read(유령 읽기) 문제는 발생함

## REPEATABLE READ

* 한 트랜잭션 내에서 같은 쿼리를 여러 번 실행했을 때, 항상 동일한 결과를 얻을 수 있음

* 첫번째 읽기 작업이 이루어진 때를 기준으로 스냅샷을 생성함

* InnoDB의 default isolation level임

## [Serializable](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_serializable)

* 어떤 한 트랜잭션이 데이터를 읽었다면, lock을 걸어 이 트랜잭션이 끝날때까지 다른트랜잭션이 읽기, 쓰기가 불가능하도록 하는 level

* autocommit이 활성화 되어있지 않으면, select 문을 select ... for share 문으로 변경함

Mysql doc을 보고 작성했으며, ANSI Isolation level이 궁금하다면 [참고](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/tr-95-51.pdf)

https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos\_isolation\_level\
https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos\_serializable
