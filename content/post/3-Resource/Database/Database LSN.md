---
date: 2023-10-11T23:20:55
updatedAt: 2024-04-21 18:34:36+3200
tags:
  - Database
  - hugo_blog
categories:
  - Database
title: Database LSN
lastmod: 2024-04-21T09:36:38.135Z
---
* Log Sequence Number의 약자
* 임의로 계속 증분하여 redo log에 저장됨
* 체크포인트 발생시 어느 LSN까지 처리되었는지 알려줌
* 로그 복구시 LSN으로 실행여부를 알 수 있음

https://www.percona.com/blog/mvcc-transaction-ids-log-sequence-numbers-and-snapshots/\
https://dba.stackexchange.com/questions/45716/what-is-log-sequence-number-how-it-is-used-in-mysql\
https://dev.mysql.com/doc/refman/8.0/en/glossary.html

\#InnoDB
