---
date: 2023-10-06T23:29:37
updatedAt: 2024-04-21 18:34:36+3240
tags:
  - Database
  - hugo_blog
  - Mysql-Trouble-shooting
categories:
  - Database
title: mysql 락걸린 쿼리 확인
lastmod: 2024-04-21T09:36:38.158Z
---
mysql 8.0\
select \* from performance\_schema.data\_locks;

https://dev.mysql.com/doc/refman/8.0/en/performance-schema-data-locks-table.html

* 테이블에 대한 설명

mysql 5.7\
select \* from information\_schema.InnoDB\_LOCKS;

\#Database\
\#Query\
\#lock\
\#Trouble-Shooting\
\#InnoDB
