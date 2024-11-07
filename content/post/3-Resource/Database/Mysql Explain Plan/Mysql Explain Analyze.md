---
date: 2024-11-06 22:19:30
updatedAt: 2024-11-07 23:27:49
tags:
  - MySQL
  - Database
  - Explain-Plan
  - hugo_blog
categories:
  - Database
title: Mysql Explain Analyze
lastmod: 2024-11-07T14:27:49.110Z
---
## Explain Analyze

* 작성한 쿼리가 시간을 소비하는 위치와 이유를 보여줌
* 쿼리를 계획하고, 계측하고 실행하면서 실행계획의 여러지점에서 행을 세고, 소요시간을 측정함
* 실행이 완료되면 Explain Analyze는 쿼리 결과 대신 계획과 측정값을 보여줌
* Explain Analyze은 Explain 쿼리 계획 검사 도구를 기반으로 구축되었으며, Explain FORMAT=TREE의 확장기능으로도 볼 수 있음
* 일반 EXPLAIN이 보여주는 쿼리 계획 및 예상 비용 외에도 Explain Analyze은 각 iterator의 실제 비용되 출력함

## 테스트

```mysql
-- 테스트 쿼리
select customer_name, sum(total_order_price)  
from `order`  
         join order_item on `order`.id = order_item.order_id  
         join product on order_item.product_id = product.id  
    where order.CREATED_AT like '202%-01%'  
group by `order`.customer_name;
```

* 아래는 위 쿼리를 explain format=TREE와 explain analyze를 사용했을때의 결과

> 테스트용 테이블 생성 DML문 및 프로시저는 맨 아래에 있으니 참고\
> 쿼리는 매번  랜덤하게 작성되므로, 상세한 결과가 다를 수 있습니다.

```sql
explain format=tree select customer_name, sum(total_order_price)
from `order`
         join order_item on `order`.id = order_item.order_id
         join product on order_item.product_id = product.id
    where product.CREATED_AT like '2020-01%'
group by `order`.customer_name;

-- explain format=TREE
-> Table scan on <temporary>
    -> Aggregate using temporary table
        -> Nested loop inner join  (cost=376372 rows=280438)
            -> Nested loop inner join  (cost=149099 rows=280438)
                -> Filter: (product.CREATED_AT like '2020-01%')  (cost=50946 rows=55423)
                    -> Index scan on product using PRODUCT__index  (cost=50946 rows=498853)
                -> Index lookup on order_item using PRODUCT_ID (PRODUCT_ID=product.ID)  (cost=1.27 rows=5.06)
            -> Single-row index lookup on order using PRIMARY (ID=order_item.ORDER_ID)  (cost=0.71 rows=1)

```

```sql
explain analyze select customer_name, sum(total_order_price)
from `order`
         join order_item on `order`.id = order_item.order_id
         join product on order_item.product_id = product.id
    where product.CREATED_AT like '2020-01%'
group by `order`.customer_name;

-- explain analyze 
-> Table scan on <temporary>  (actual time=207..207 rows=5 loops=1)
    -> Aggregate using temporary table  (actual time=207..207 rows=5 loops=1)
        -> Nested loop inner join  (cost=376397 rows=280438) (actual time=0.451..205 rows=4327 loops=1)
            -> Nested loop inner join  (cost=149125 rows=280438) (actual time=0.416..194 rows=4327 loops=1)
                -> Filter: (product.CREATED_AT like '2020-01%')  (cost=50971 rows=55423) (actual time=0.103..160 rows=4251 loops=1)
                    -> Table scan on product  (cost=50971 rows=498853) (actual time=0.0855..96.6 rows=500000 loops=1)
                -> Index lookup on order_item using PRODUCT_ID (PRODUCT_ID=product.ID)  (cost=1.27 rows=5.06) (actual time=0.00735..0.00799 rows=1.02 loops=4251)
            -> Single-row index lookup on order using PRIMARY (ID=order_item.ORDER_ID)  (cost=0.71 rows=1) (actual time=0.00226..0.00228 rows=1 loops=4327)

```

* explain format=TREE와 explain analyze는 유사해보이지만, 차이는 실제 쿼리에 소모한 시간과, 비용이 출력된다는 것
* 추가되는 정보
  * 첫번째 row를 가져오는 실제 시간(밀리초)
  * 마지막 row를 가져오는 실제 시간(밀리초)
  * 실제 읽는 row 수
  * 실제로 도는 loop 수
* 13~15번째 줄의 데이터를 살펴보면, 예상된 비용은 50971이고, 55423개의 row를 반환한다는것으로 추측 가능
  * 이 추정치는 쿼리가 실행되기전 optimizer에서 사용가능한 통계를 기반으로 만들어짐
  * explain format=TREE 출력에도 존재함
* 결과는 가장 안쪽의 쿼리가 먼저 실행되며, 깊이가 같다면 위쪽의 쿼리가 먼저 실행된 것임

### explain analyze 결과 분석

```sql
-> Filter:Index lookup on order_item using PRODUCT_ID 
(PRODUCT_ID=product.ID)  
(cost=1.27 rows=5.06) 
(actual time=0.00735..0.00799 rows=1.02 loops=4251)
```

* 15번째 line의 루프수 부터 보면,
  * 12번째 줄에서 Nested loop join을 사용하는 것을 확인 가능함
  * Index lookup의 loop수는 4251개
  * 왜 4251개 일지 확인해 보면, 우선 조건에 맞는 상품의 갯수는 4251개
  * 먼저 해당 조건에 맞는 product들을 상품 테이블에 조회(4251개)
  * 해당 product\_id를 가지는 row를 order\_item테이블의 각 row를 가져와서 비교
  * order\_item테이블을 순회하면서 첫번째 product id를 가지는 order\_item을 찾음
  * order\_item테이블을 순회하면서 두번째 product id를 가지는 order\_item을 찾음
  * ...
  * 이렇게 상품이 4251개 이기에 order\_item테이블을 4251번 순회하면서 데이터를 가져옵니다

```sql
-> Nested loop inner join  (cost=149125 rows=280438) (actual time=0.416..194 rows=4327 loops=1)
	-> Filter: (product.CREATED_AT like '2020-01%')  (cost=50971 rows=55423) (actual time=0.103..160 rows=4251 loops=1)
		-> Table scan on product  (cost=50971 rows=498853) (actual time=0.0855..96.6 rows=500000 loops=1)
	-> Index lookup on order_item using PRODUCT_ID (PRODUCT_ID=product.ID)  (cost=1.27 rows=5.06) (actual time=0.00735..0.00799 rows=1.02 loops=4251)
```

> 이제 바로 위 결과를 보시면 됩니다 : )

* explain analyzer의 가장 재밌는 정보는 actual time임
  * "0.00735..0.00799"은 실제 시간으로 첫번째 행을 읽는데 평균 0.00735ms, 모든 행을 읽는데 평균 0.00799ms가 걸렸다는 의미
  * loop때문에, 해당 시간은 4251번 루프를 돈 것의 평균
  * index lookup의 실제 시간은 4251번을 곱한 것
  * 시간 계산 법
    1. 가장 안쪽(line 3), table scan시 모든 행을 가져오는데 걸리는 시간은 96.6 \* 1 = 96.6초
    2. 그 위의 Filter하는 로직(line 2)에서는 160초 = 96.6초(1의 결과) + filter 시간
    3. index lookup시(line 4) 모든 행을 가져오는 시간 0.00799 \* 4251 = 33.96549
    4. nested loop inner join(line 1)의 모든 행을 가져오는 시간 194초 = 160초(2의결과) + 33.96549(3의 결과)

#### 위의 결과로 executer가 어디서 시간 소비를 많이하는지 찾아보고 수정하면 좋음!

### 추가로 고려하면 좋은 도구

* 쿼리 분석 : EXPLAIN FORMAT=TREE
* 쿼리 실행 프로파일 : EXPLAIN ANALYZE
* 쿼리 플랜을 보기 위한 도구 : [Optimizer trace](https://dev.mysql.com/doc/internals/en/optimizer-tracing.html)

```sql
-- 기존 테이블 삭제  
DROP TABLE IF EXISTS ORDER_ITEM;  
DROP TABLE IF EXISTS `ORDER`;  
DROP TABLE IF EXISTS PRODUCT;  
  
-- PRODUCT 테이블 생성  
CREATE TABLE PRODUCT (  
    ID INT AUTO_INCREMENT PRIMARY KEY,  
    LABEL VARCHAR(255) NOT NULL,  
    PRICE DECIMAL(10,2) NOT NULL,  
    FIELD VARCHAR(255) NOT NULL,  
    CREATED_AT DATETIME NOT NULL  
);  
create index PRODUCT_CREATED_AT_index  
    on PRODUCT (CREATED_AT);
  
-- ORDER 테이블 생성  
CREATE TABLE `ORDER` (  
    ID INT AUTO_INCREMENT PRIMARY KEY,  
    CUSTOMER_NAME VARCHAR(255) NOT NULL,  
    SHIPPING_COST DECIMAL(10,2) NOT NULL,  
    TOTAL_ORDER_PRICE DECIMAL(10,2) NOT NULL,  
    CREATED_AT DATETIME NOT NULL  
);  
  
-- ORDER_ITEM 테이블 생성  
CREATE TABLE ORDER_ITEM (  
    ID INT AUTO_INCREMENT PRIMARY KEY,  
    ORDER_ID INT NOT NULL,  
    PRODUCT_ID INT NOT NULL,  
    PRODUCT_NAME VARCHAR(255) NOT NULL,  
    PRICE DECIMAL(10,2) NOT NULL,  
    QUANTITY INT NOT NULL,  
    TOTAL_PRICE DECIMAL(10,2) NOT NULL,  
    CREATED_AT DATETIME NOT NULL,  
    FOREIGN KEY (ORDER_ID) REFERENCES `ORDER`(ID),  
    FOREIGN KEY (PRODUCT_ID) REFERENCES PRODUCT(ID)  
);  
  
  
DELIMITER //  
  
CREATE PROCEDURE POPULATE_TABLES()  
BEGIN  
    DECLARE i INT DEFAULT 1;  
    DECLARE j INT DEFAULT 1;  
    DECLARE item_count INT;  
    DECLARE shipping DECIMAL(10,2);  
    DECLARE total DECIMAL(10,2);  
    DECLARE product_price DECIMAL(10,2);  
    DECLARE order_id INT;  
    DECLARE product_id INT;  
    DECLARE price DECIMAL(10,2);  
    DECLARE quantity INT;  
    DECLARE total_price DECIMAL(10,2);  
    DECLARE created_at DATETIME;  
  
    -- 1. PRODUCT 테이블에 데이터 삽입  
    WHILE i <= 500000 DO  
        SET created_at = DATE_ADD('2020-01-01', INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND;  
  
        INSERT INTO PRODUCT (LABEL, PRICE, FIELD, CREATED_AT)  
        VALUES (  
            CONCAT('Product ', i),  
            ROUND(RAND() * 1000, 2), -- 가격: 0 ~ 1000 사이의 랜덤 값  
            CONCAT('Field ', FLOOR(RAND() * 10) + 1), -- 필드: Field 1 ~ Field 10  
            created_at  
        );  
  
        SET i = i + 1;  
  
        -- 진행 상황 출력 (선택 사항)  
        IF MOD(i, 10000) = 0 THEN  
            SELECT CONCAT('Inserted ', i, ' PRODUCTS');  
        END IF;  
    END WHILE;  
  
    SET i = 1;  
  
    -- 2. ORDER 테이블에 데이터 삽입  
    WHILE i <= 500000 DO  
        SET shipping = ROUND(RAND() * 50, 2); -- 배송비: 0 ~ 50 사이의 랜덤 값  
  
        -- 총 주문 가격을 배송비로 초기화  
        SET total = shipping;  
  
        -- 임의의 상품 수 (1 ~ 5개)를 선택하여 총 가격 계산  
        SET item_count = FLOOR(RAND() * 5) + 1;  
        SET j = 1;  
  
        WHILE j <= item_count DO  
            SET product_price = ROUND(RAND() * 1000, 2);  
            SET total = total + product_price;  
            SET j = j + 1;  
        END WHILE;  
  
        -- 무작위로 고객 이름 할당 (5명)  
        SET @rand_num = FLOOR(1 + RAND() * 5);  
        SET @customer = CASE @rand_num  
            WHEN 1 THEN 'Customer A'  
            WHEN 2 THEN 'Customer B'  
            WHEN 3 THEN 'Customer C'  
            WHEN 4 THEN 'Customer D'  
            ELSE 'Customer E'  
        END;  
  
        -- 무작위 생성 날짜  
        SET created_at = DATE_ADD('2020-01-01', INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND;  
  
        INSERT INTO `ORDER` (CUSTOMER_NAME, SHIPPING_COST, TOTAL_ORDER_PRICE, CREATED_AT)  
        VALUES (  
            @customer,  
            shipping,  
            total,  
            created_at  
        );  
  
        SET i = i + 1;  
  
        -- 진행 상황 출력 (선택 사항)  
        IF MOD(i, 10000) = 0 THEN  
            SELECT CONCAT('Inserted ', i, ' ORDERS');  
        END IF;  
    END WHILE;  
  
    SET i = 1;  
  
    -- 3. ORDER_ITEM 테이블에 데이터 삽입  
    WHILE i <= 500000 DO  
        SET order_id = FLOOR(RAND() * 100000) + 1; -- 1 ~ 100000  
        SET product_id = FLOOR(RAND() * 100000) + 1; -- 1 ~ 100000  
        SET price = ROUND(RAND() * 1000, 2); -- 0 ~ 1000  
        SET quantity = FLOOR(RAND() * 5) + 1; -- 1 ~ 5  
        SET total_price = ROUND(price * quantity, 2);  
        SET created_at = DATE_ADD('2020-01-01', INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND;  
  
        INSERT INTO ORDER_ITEM (ORDER_ID, PRODUCT_ID, PRODUCT_NAME, PRICE, QUANTITY, TOTAL_PRICE, CREATED_AT)  
        VALUES (  
            order_id,  
            product_id,  
            CONCAT('Product ', product_id),  
            price,  
            quantity,  
            total_price,  
            created_at  
        );  
  
        SET i = i + 1;  
  
        -- 진행 상황 출력 (선택 사항)  
        IF MOD(i, 10000) = 0 THEN  
            SELECT CONCAT('Inserted ', i, ' ORDER_ITEMS');  
        END IF;  
    END WHILE;  
  
END //  
  
DELIMITER ;  
  
CALL POPULATE_TABLES();
```
