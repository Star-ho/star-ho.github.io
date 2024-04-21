---
date: 2023-10-03T09:58:50
updatedAt: 2024-04-21 18:34:36+3240
tags:
  - Database
  - hugo_blog
categories:
  - Database
title: Sargable query
lastmod: 2024-04-21T09:36:38.157Z
---
* 'Search Argument-able'의 약자
* DBMS 엔진이 인덱스를 활용해서 실행 속도를 높일 수 있는 쿼리를 말함
* 연산자

```
SARGable predicates include the following operators
=, >, >=, <, <=, IN, BETWEEN, and LIKE (in the case of prefix matching)
Non-SARGable operators include
NOT, NOT IN, <>, and LIKE 
```

* 컬럼을 함수로 감싸지 않은것

```
Bad: Select ... WHERE isNull(FullName,'Ed Jones') = 'Ed Jones'
Fixed: Select ... WHERE ((FullName = 'Ed Jones') OR (FullName IS NULL))

Bad: Select ... WHERE SUBSTRING(DealerName,4) = 'Ford'
Fixed: Select ... WHERE DealerName Like 'Ford%'

Bad: Select ... WHERE DateDiff(mm,OrderDate,GetDate()) >= 30
Fixed: Select ... WHERE OrderDate < DateAdd(mm,-30,GetDate()) 
```

> 생각해보면 간단한듯! 컬럼에 함수를 씌우면 컬럼마다 함수를 실행해야하니 속도가 느려짐

<https://en.wikipedia.org/wiki/Sargable>\
<https://stackoverflow.com/questions/799584/what-makes-a-sql-statement-sargable>

\#Query-turning
