---
date: 2023-10-02T23:31:55
updatedAt: 2024-04-21 18:34:36+3210
tags:
  - Database
  - hugo_blog
categories:
  - Database
title: Clustered Index, non-Clustered Index
lastmod: 2024-04-21T09:36:38.144Z
---
테이블에 대한 색인을 만들때,\
해당하는 색인에 데이터를 같이 놓는다면, 색인을 찾은 후 바로 데이터를 가져올 수 있습니다\
색인과 테이블을 따로 만들면 색인 에있는 데이터의 위치를 가지고 다시 데이터를 찾아야합니다

색인이 여러개일때 색인과 데이터를 같이 놓는다면 데이터의 중복이 발생하고, 읽기 성능은 좋아지겠지만 쓰기성능이 저하됩니다.\
그래서 하나의 색인에만 데이터를 같이놓고(clustered index), 다른색인에는 색인과 데이터의 위치(non-clutered-index)를 가지고 있습니다ㅇ

index는 key-value 구조로, key는 index의 값입니다.\
value는 clustered index에서는 해당하는 데이터값(row)이고 non-clutered-index의 값은 해당하는 clustered index의 키값입니다\
clustered Index의 값이 길면, non-clutered-index는 각 인덱스마다 긴 clutered-index를 가져야해서 저장공간이 늘어 좋지않습니다

## Clustering Index

* 데이터가 물리적으로 저장될떄 정렬 방식을 정의
* 테이블당 하나만 존재 가능
* 기본키 제약조건이 걸린 컬럼에 자동으로 생성됨

## non-Clustering Index

* 데이터를 물리적으로 정렬하지 않음
* 테이블과 다른 물리적 공간에 저장됨
* 테이블에 여러개가 존재 가능
* 유니크 제약조건에 걸린 컬럼에 자동으로 적용됨
* 너무 많이 생성시 생성, 수정, 삭제의 오버헤드가 커짐

<https://www.sqlshack.com/what-is-the-difference-between-clustered-and-non-clustered-indexes-in-sql-server/>\
<https://learn.microsoft.com/ko-kr/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver16>\
<https://gwang920.github.io/database/clusterednonclustered/>\
https://mozi.tistory.com/320\
https://dev.mysql.com/doc/refman/8.0/en/InnoDB-index-types.html

\#InnoDB
