---
date: 2024-03-02T22:41:04
updatedAt: 2024-09-22 16:07:28
tags:
  - Concurrency
  - hugo_blog
  - Concept
  - Database
categories:
  - Concurrency
  - Concurrent-series
title: 9-Record Locks
lastmod: 2024-09-22T07:07:28.889Z
---
* record의 index에 거는 lock

> primary index가 없는 테이블에도 가능\
> primary index가 없더라도 mysql에서는 hidden clustered index를 생성하기 때문

> Mysql Doc에는 row락이라는 용어가 등장하지 않음,\
> 스택오버플로우에는 record lock과 row lock이 동일 하다는 이야기가 있음\
> https://stackoverflow.com/questions/74967004/row-level-locks-vs-index-record-locks

https://dev.mysql.com/doc/refman/8.0/en/InnoDB-locking.html
