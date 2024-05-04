---
date: 2024-05-03 23:34:03+0740
updatedAt: 2024-05-04 20:51:47+9900
tags:
  - MySQL
  - Explain-Plan
  - hugo_blog
categories:
  - Database
title: Mysql Explain Plan(조인타입) - 2
lastmod: 2024-05-04T11:51:47.990Z
---
## 조인타입

먼저에 나올수록 성능이 좋음

* system
  * 테이블이 하나의 row만 가지고 있을때 표시됨
  * const join타입의 특별한 케이스임

* const
  * 쿼리시작시 결과가 오직 하나일때 표시됨
  * 결과가 하나의 row일 경우, 컬럼의 값은 옵티마이저에 의해 상수로 처리됨
  * const 테이블은 오직 한번만 읽기에 매우 빠름
  * 기본키 또는 UNIQUE 인덱스의 모든 부분을 상수값과 비교될때 표시됨
  * 예제 쿼리

```sql
SELECT * FROM _tbl_name_ WHERE _primary_key_=1; 
SELECT * FROM _tbl_name_ WHERE _primary_key_part1_=1 AND _primary_key_part2_=2;
```

* eq\_ref
  * const와 system을 제외하고는 가장 최상의 join
  * 이전 테이블에서 가져온 행 조합마다 해당 테이블에서 정확히 한 행을 읽음
  * 인덱스의 모든 부분이 join에 사용되며, 인덱스가 기본키 또는 유니크키+not null조합이면서 `=`를 사용하여 비교될때 사용됨
  * 비교하는 값은 상수이거나, 이전에 읽은 테이블의 컬럼 사용
  * 예제쿼리

```SQL
SELECT * FROM _ref_table_,_other_table_ WHERE _ref_table_._key_column_=_other_table_._column_; 
SELECT * FROM _ref_table_,_other_table_ 
	WHERE _ref_table_._key_column_part1_=_other_table_._column_ AND _ref_table_._key_column_part2_=1;
```

* ref
  * 이전 테이블에 가져온 행 조합마다일치하는 모든 인덱스를 읽음
  * 키의 접두사만 읽거나, 키가 기본키나 유니크키가 아닐 경우 사용됨
    * 정확히 하나의 row를 반환하지 않는 경유를 의미
  * 키에 해당하는 행이 적은경우, 충분히 좋은 join 방법임
  * `=`이거나 `<=>`일때 사용됨
  * 예제쿼리

```SQL
SELECT * FROM _ref_table_ WHERE _key_column_=_expr_; 
SELECT * FROM _ref_table_,_other_table_ WHERE _ref_table_._key_column_=_other_table_._column_; 
SELECT * FROM _ref_table_,_other_table_ 
	WHERE _ref_table_._key_column_part1_=_other_table_._column_ AND _ref_table_._key_column_part2_=1;
```

* fulltext
  * FULLTEXT인덱스를 사용할때 사용됨

* ref\_or\_null
  * ref와 유사하지만, null을 포함하는 행의 질의를 할때 사용됨
  * subquery를 처리할때 자주 표시됨
  * 예제쿼리

```SQL
SELECT * FROM _ref_table_ WHERE _key_column_=_expr_ OR _key_column_ IS NULL;
```

* index\_merge
  * 인덱스 병합 최적화일때 사용됨
  * 이 경우 Explain의 key column에는 사용된 인덱스 목록이 포함되며, key\_len에는 가장 긴 인덱스의 키 목록이 표함됨
  * [링크](https://dev.mysql.com/doc/refman/8.0/en/index-merge-optimization.html)참고

* unique\_subquery
  * IN절 내의 eq\_ref 서브쿼리의를 대체함
  * 효율성을 높이기 위해 서브쿼리를 대체하여 인덱스만 조회함
  * 예제쿼리

```sql
value IN (SELECT primary_key FROM single_table WHERE some_expr)
```

* index\_subquery
  * unique\_subquery와 유사하지만, non-unique 서브쿼리일때 사용됨
  * 예제쿼리

```sql
value IN (SELECT key_column FROM single_table WHERE some_expr)
```

* range
  * 인덱스로 범위질의를 사용시 사용됨
  *  =, <>, >, >=, <, <=, IS NULL, <=>, BETWEEN, LIKE, IN() 연산자를 사용하여 상수 값과 비교할때 나타남
  * key\_len에는 사용된 키 중 가장 긴키의 길이를 표시함
  * 예제쿼리

````sql
    SELECT * FROM tbl_name
      WHERE key_column = 10;
    
    SELECT * FROM tbl_name
      WHERE key_column BETWEEN 10 and 20;
    
    SELECT * FROM tbl_name
      WHERE key_column IN (10,20,30);
    
    SELECT * FROM tbl_name
      WHERE key_part1 = 10 AND key_part2 IN (10,20,30);
    ```

- index
	- 인덱스 트리가 스캔되는 것을 제외하면, ALL과 동일함
	- 아래 두가지 경우에 나타남
		- 인덱스 쿼리가 커버링 인덱스이고, 테이블에서 필요한 모든 데이터를 인덱스에서 가져올 수 있는 경우, 인덱스 트리만 스캔함
			- Extra column에 Using Index라고 나타남
			- index Only 스캔은 인덱스만 스캔하므로, 모든 테이블 데이터를 스캔하는 ALL보다 항상 빠름
		- 인덱스를 사용하여 모든 테이블 데이터를 인덱스 순서대로 접근하는 경우
			- Extra column에 Using Index라고 나타나지 않음
	- 쿼리가 단일 인덱스의 일부 열만 사용하는 경우 이 조인 유형이 나타남

- ALL
	- 이전의 모든 테이블 행 조합마다 테이블 전체 테이블을 스캔함
	- 거의 모든 경우 좋지않음
	- index를 추가하여 피할 수 있음
````
