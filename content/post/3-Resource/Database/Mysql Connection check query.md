---
date: 2024-03-31T22:41:00
updatedAt: 2024-04-21 18:32:05+2370
tags:
  - Trouble-Shooting
  - Database
  - hugo_blog
categories:
  - Database
title: Mysql Connection check query
lastmod: 2024-04-21T09:32:12.852Z
---
* 커넥션 관련 쿼리

```
-- 현재 mysql server에서 동시에 지원가능한 connection 가능 개수
show variables like '%max_connections%';

-- 서버가 시작된 후로 동시에 연결된 최대 connection 개수
SHOW STATUS WHERE `variable_name` = 'Max_used_connections';  

-- 현저 서버에 연결된 connection 수
SHOW STATUS WHERE `variable_name` = 'Threads_connected';
```

\#MySQL
