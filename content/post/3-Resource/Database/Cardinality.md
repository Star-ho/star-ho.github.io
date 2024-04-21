---
date: 2023-10-02T23:32:28
updatedAt: 2024-04-21 18:34:36+3200
tags:
  - Database
  - hugo_blog
categories:
  - Database
title: Cardinality
lastmod: 2024-04-21T09:36:38.139Z
---
* 각각의 컬럼들은 요구사항에 따라 카디널리티가 다름
* 카디널리티가 높다
  * 모든 값들이 다를때
* 카디널리티가 낮다
  * 모든 값들이 같을때
* 여러 컬럼을 인덱스로 만들때, 카디널리티가 높은순에서 낮은순으로 설정할것
  * 필터링 되는 숫자가 다르기 때문
* 카디널리티는 데이터베이스가 정렬하거나, 검색시 성능에 영향을 끼침기에 데이터베이스가 최적의 플랜을 짜기한 중요한 요소임

> 인덱스는 데이터베이스에서 어떤 요소를 빨리 찾기 위한 자료구조임\
> 카디널리티가 낮은 컬럼을 인덱스로 잡는다면 하나의 인덱스에 많은 데이터가 있을것이고\
> 카디널리티가 높은 컬럼을 인덱스로 잡는다면 하나의 인덱스에 적인 데이터가 있겠지?\
> 이런것들을 고려해서 인덱스로 잡으면 좋겠다!

<https://stackoverflow.com/questions/2566211/what-is-cardinality-in-mysql>\
<https://learn.microsoft.com/en-us/sql/relational-databases/performance/cardinality-estimation-sql-server?view=sql-server-ver16>

\#Database\
\#Definition
