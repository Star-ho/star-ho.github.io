---
date: 2023-10-02T23:30:52
updatedAt: 2024-04-21 18:34:36+3210
tags:
  - Database
  - hugo_blog
categories:
  - Database
title: Covering Indexes(커버링 인덱스)
lastmod: 2024-04-21T09:36:38.143Z
---
* 하나의 쿼리의 전체 요구사항이 인덱스인 상황
* 커버링 인덱스를 활용하므로서 실제 테이블에 접근하지 않고, Index tree만 검색 후 결과를 리턴
* 실제 테이블을 접근하지 않음으로 속도가 매우 빠름
* 출력되어야 하는 값까지 모두 인덱스일경우

<https://planetscale.com/learn/courses/mysql-for-developers/indexes/covering-indexes>\
<https://www.sqler.com/board_SQLQA/707203>

\#Database
