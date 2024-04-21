---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8750
tags:
  - Concurrency
  - Concept
  - hugo_blog
  - Database
categories: Concurrency
title: 7-Shared Lock(S-lock)
lastmod: 2024-04-21T09:36:36.390Z
---
* 특정 행을 읽기위해 거는 락
* 어떤 object에 S-lock이 걸려있다면 다른 트랜잭션에서 읽기는 가능하지만 변경은 불가능함
* S-lock이 걸려있는 object에 S-lock를 또 걸 수 있지만 X-lock은 걸 수 없음

https://dev.mysql.com/doc/refman/8.0/en/InnoDB-locking.html
