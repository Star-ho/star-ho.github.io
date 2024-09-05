---
date: 2024-05-03 23:35:47
updatedAt: 2024-07-01 13:26:52
tags:
  - hugo_blog
  - Explain-Plan
  - MySQL
categories:
  - Database
title: Mysql Explain Plan(Extra Information) - 3
lastmod: 2024-07-01T04:26:52.244Z
---
* Extra 열에는 Mysql에서 쿼리를 resolve하기 위한 추가적인 정보가 들어감
* 여기서는 추가적인 정보 중 유용하다고 생각되는 정보를 정리할 예정
* 전체 정보는 다음 \[링크]를 참고할것

## Using file sort

* 정렬된 순서로 행을 찾기 위해 추가적인 작업이 필요하다는 것을 의미함
* 정렬은 조인 유형에 따라 모든 행을 살펴보고, Where 절과 일치하는 모든 행에 대해 정렬키와 포인터를 저장하는 방식으로 수행됨
  * 이후 키가 정렬되고 정렬된 순서대로 행을 검색함
* 부하가 많이 걸리는 부분으로 개선이 필요함

## Using index

* 커버링 인덱스가 사용되었음을 의미함
* 실제 테이블데이터를 읽지 않고, 인덱스만 조회함
* 쿼리가 하나의 인덱스에서 조회에 필요한 모든 데이터를 가지고 있을 경우 사용됨

## Using index condition

* 인덱스를 먼저 읽은 후 필요할 경우 테이블 데이터를 읽음
* 테이블 데이터를 필요할때 까지 읽는것을 미룸
* [링크](https://dev.mysql.com/doc/refman/8.0/en/index-condition-pushdown-optimization.html)참고

## Using index for group-by

* Using index와 유사하게, 실제 테이블데이터를 읽지않고 인덱스에서 group by 또는 distinct에 필요한 모든 열을 찾았음을 의미
* [그룹바이 최적화](https://dev.mysql.com/doc/refman/8.0/en/group-by-optimization.html)참고

## Using join buffer (Block Nested Loop), Using join buffer (Batched Key Access), Using join buffer (hash join) 

* 이전 조인 테이블을 버퍼에서 부분적으로 읽은 후 버퍼데이터와 해당 테이블의 조인을 실행함
* 각 buffer는 괄호안의 알고리즘을 사용함

## Using temporary

* 해당 쿼리를 수행하기 위해서 임시 테이블이 사용됨을 의미
* 주로 GROUP by나 order by를 사용하는 쿼리에서 보여짐

## Using where

* where절을 사용하여 행을 제한한것을 의미함
* 모든 행을 가져오는 경우가 아니라면, using where이 사용되지 않으면서 join type이 ALL이나 index이면 쿼리에 문제가 있는 것일 수 있음

## Using index for skip scan

```sql
CREATE TABLE t1 (f1 INT NOT NULL, f2 INT NOT NULL, PRIMARY KEY(f1, f2));
INSERT INTO t1 VALUES
  (1,1), (1,2), (1,3), (1,4), (1,5),
  (2,1), (2,2), (2,3), (2,4), (2,5);
INSERT INTO t1 SELECT f1, f2 + 5 FROM t1;
INSERT INTO t1 SELECT f1, f2 + 10 FROM t1;
INSERT INTO t1 SELECT f1, f2 + 20 FROM t1;
INSERT INTO t1 SELECT f1, f2 + 40 FROM t1;
ANALYZE TABLE t1;

EXPLAIN SELECT f1, f2 FROM t1 WHERE f2 > 40;
```

* 11번 라인의 쿼리문이 실행되면 Using index for skip scan이 적용됨
* 11번 라인의 쿼리는 인덱스가 f1, f2가 걸려있는데, f2만 사용해서 조건을 걸었음
  * f1만을 사용하거나, f1, f2를 사용하면 Using Index로 나타남
* 11번 쿼리는 f1의 인덱스는 뛰어넘고, f2 인덱스 만 사용해서 결과를 가져옴
* f1의 인덱스에서 고유값을 찾고, 고유값마다 f2의 인덱스에 걸린 조건을 찾아 결과를 가져옴
  * 단일 인덱스에 대한 여러 개별 검색이 이루어지고, 결합시 접두사 열의 영향이 제거됨

## Using join buffer(Block Nested Loop, hash join)
