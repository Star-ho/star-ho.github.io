---
date: 2024-03-02T22:41:03
updatedAt: 2024-09-21 23:55:46
tags:
  - Concurrency
  - Concept
  - hugo_blog
  - Database
categories:
  - Concurrency
title: 8-Intention shared lock, exclusive lock
lastmod: 2024-09-21T14:55:46.152Z
---
* IS, IX lock과 S,X Lock과의 차이는 테이블까지 락이 걸림

* IS, IX lock을 걸면 해당 테이블에도 락이 걸림

* 해당 테이블에 대해 다른 트래잭션에서 같은 테이블의 다른 row에 IS,IX락은 걸 수 있지만, 같은 테이블에 대해 S,X Lock을 걸 수 없음(실험완료)
  * S,X와 같이, IS가 걸려있으면 해당 테이블에 대해 S락은 가능, X락은 불가
  * S,X와 같이, IX가 걸려있으면 해당 테이블에 대해 S,X락 둘다 불가
  * 읽기나 쓰기 도중 테이블이 변경되는것을 막기 위함이라고 추측함

* `SELECT ... FOR SHARE`로 S Lock을, `SELECT ... FOR UPDATE`로 X락을 걸 수 있음

> - IS lock추가\
>   \- select .. lock in share mode ;\
>   \- select ..  for share ;\
>   \- 현재(8.0 기준) for share가 lock in share mode를 대체 하려하나 하위호환성 보장을 위해 lock in share mode를 유지하는중\
>   \- 그러나, for share를 사용하면 OF table\_name, NOWAIT, and SKIP LOCKED를 사용할 수 있음
>
> SELECT ... FOR SHARE is a replacement for SELECT ... LOCK IN SHARE MODE, but LOCK IN SHARE MODE remains available for backward compatibility. The statements are equivalent. However, FOR SHARE supports OF table\_name, NOWAIT, and SKIP LOCKED options. See Locking Read Concurrency with NOWAIT and SKIP LOCKED.

https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos\_shared\_lock

https://dev.mysql.com/doc/refman/8.0/en/InnoDB-locking.html
