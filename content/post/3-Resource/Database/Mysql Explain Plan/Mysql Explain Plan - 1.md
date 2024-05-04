---
date: 2024-04-30 22:35:11
updatedAt: 2024-05-04 22:51:25
tags:
  - hugo_blog
  - MySQL
  - Explain-Plan
categories:
  - Database
title: Mysql Explain Plan - 1
lastmod: 2024-05-04T13:51:25.094Z
---
## 개요

* Explain 문은 Mysql이 어떻게 statements를 실행할것인가에 대한 정보를 제공
  * select, delete, insert, replace, update문에 적용 가능
* 출력에 테이블이 나열되는 순서는, Mysql이 statements를 처리하는 동안 테이블을 읽는 순서임
  * 첫번째 테이블에서 행을 읽고, 두번째테이블에서 일치하는 행을 찾고, 세번째에서 또 찾는 방식임
* 모든 테이블이 처리되면 Mysql은 선택한 열을 출력하고, 더 많은 행을 찾기 찾을 때 까지 테이블 목록을 역추적함

## Explain Output Columns

| Column         | JSON Name      | Meaning               |
| -------------- | -------------- | --------------------- |
| id             | select\_id     | Select 식별자            |
| select\_type   | None           | Select Type           |
| table          | table\_name    | 결과 행을 가져오는 테이블        |
| partitions     | partitions     | 매칭되는 파티션              |
| type           | access\_type   | join 유형               |
| possible\_keys | possible\_keys | 선택될 수 있는 인덱스          |
| key            | key            | 실제로 선택된 인덱스           |
| key\_len       | key\_length    | 선택된 키의 길이             |
| ref            | ref            | 인덱스와 비교한 열            |
| rows           | rows           | 예상되는 행의 추정치           |
| filtered       | filtered       | 테이블 조건에 따라 필터링된 행의 비율 |
| Extra          | None           | 추가정보                  |

## id

* Select의 식별자로, 쿼리 내 Select의 일련 번호
* 다른행 의 유니온 결과를 참조하는 경우 Null일수 있음
  * 이 경우 table Column은 \<union M,N> 으로 나타남

## select\_type

* Select의 유형으로 다음 표에 나타나는 것중 하나가 들어감

| select\_type         | JSON Name                    | Meaning                                                      |
| -------------------- | ---------------------------- | ------------------------------------------------------------ |
| SIMPLE               | None                         | 서브쿼리나 Union을 사용하지 않은 간단한 Select                              |
| PRIMARY              | None                         | 가장 바깥쪽의 SELECT                                               |
| UNION                | None                         | UNION으로 결합하는 첫번째를 제외한 두번째 이후의 쿼리                             |
| DEPENDENT UNION      | dependent (true)             | UNION으로 결합하는 첫번째를 제외한 두번째 이후의 쿼리이지만 외부 쿼리의 영향을 받는 Select     |
| UNION RESULT         | union\_result                | UNION의 결과일때                                                  |
| SUBQUERY             | None                         | Subquery의 첫번째 결과, From절 이외에서 사용되는 서브쿼리                       |
| DEPENDENT SUBQUERY   | dependent (true)             | 외부 쿼리에 의존하는 Subquery의 첫번째 결과,<br>From절 이외에서 사용되는 서브쿼리        |
| DERIVED              | None                         | Select쿼리의 실행결과로 메모리나 디스크에 임시 저장되는 정보                         |
| DEPENDENT DERIVED    | dependent (true)             | 다른 테이블에 의존하고 있는 DERIVED 테이블                                  |
| MATERIALIZED         | materialized\_from\_subquery | 서브쿼리의 내용을 임시테이블로 구체화 할때 사용                                   |
| UNCACHEABLE SUBQUERY | cacheable (false)            | 결과를 캐시할 수 없고, 외부 쿼리의 각 row에 대해 재평가가 필요한 데이터에 일때 표시됨          |
| UNCACHEABLE UNION    | cacheable (false)            | 캐시를 할 수 없는 서브쿼리를 포함하는, 첫번째를 제외한 두번째 이후의 UNION select일 경우 표시됨 |

* select쿼리가 아닌 CUD쿼리는 해당 statements의 종류가 표시됨
  * DELETE일 경우, select\_type에는 DELETE를 표시

## table

* 각 행의 결과를 가져오는 테이블의 이름
* \<union`M`,`N`>
  * id(Explain의 id 컬럼)가 M과 N인 행의 UNION결과를 나타냄
* \<derived`N`>
  * id(Explain의 id 컬럼)가 N인 쿼리의 결과로 파생된  행
  * derived테이블은 FROM에 있는 서브쿼리의 결과임
* \<subquery`N`>
  * id(Explain의 id 컬럼)가 N인 행에 대한 구체화된 subquery의 결과를 나타냄

### subquery mareirialzied 최적화 [링크](https://dev.mysql.com/doc/refman/8.0/en/subquery-materialization.html)

## partitions

* 쿼리에 의해 매치된 파티션을 나타냄
* 파티션이 없는 테이블은 NULL로 표시됨

## type(join 유형)

* join type을 나타냄

## possible\_keys

* MySQL이 해당 테이블에서 데이터를 찾기위해 선택할 수 있는 인덱스를 보여줌
* EXPLAIN문과는 독립적이어서, possible\_keys에 나타난 인덱스를 실제로 사용할 수 없을 수 있음
* 해당 행이 NULL일경우 관련 인덱스가 없다는걸 난타냄
  * 이 경우 WHERE절을 참고해 컬럼에 인덱스를 지정하여 쿼리 성능을 개선할 수 있음

## key

* 실제로 사용을 결정한 인덱스를 보여줌

## key\_len

* 사용하기로 결정된 key의 길이를 나타냄

## ref

* key에서 선택된 인덱스와 비교되는 컬럼 또는 상수값을 나타냄

## rows

* MySQL이 해당 쿼리를 실행하기 위해 검사해야 한다고 생각되는 행의 수를 나타냄
* innodb의 경우 항상 추정치이며 정확하지 않을 수 있음

## filtered

* 테이블의 조건에 따라 필터링 되는 행의 수를 퍼센트로 나타낸것
* 최대값은 100이며 필터링 되지 않은 것을 의미함

## Extra

* Mysql이 쿼리를 해결하는데 필요한 추가정보를 나타냄

Real Mysql 8.0\
https://dev.mysql.com/doc/refman/8.0/en/explain-output.html\
https://zzang9ha.tistory.com/436
