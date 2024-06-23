---
date: 2024-05-22 22:43:24
updatedAt: 2024-06-23 21:05:06
tags:
  - InnoDB
  - InnoDB-File-Structure
  - hugo_blog
categories:
  - Database
title: 5-physical structure of records in InnoDB
lastmod: 2024-06-23T12:05:06.497Z
---
## Record

* 해당 포스트에서는 COMPACT row format만 고려함

### Record offsets

* 이전 포스트에서 레코드 오프셋은 레코드를 가르키는 구조라고 설명했음
* 레코드 오프셋은, 가변길이인 레코드 데이터 자체의 시작을 가르키지만, 각 레코드 앞에는 가변길이의 레코드 헤더가 존재함
* 해당 포스트의 글과 그림에서 레코드 데이터는 N에 존재하고, N+1과같이 양수오프셋으로 표현함
  * 헤더는 N-1과 같이 음수 오프셋으로 표현함
* InnoDB는 종종 레코드의 시작점 위치인 N을 원본으로 지칭함

### The record header

![center](/image/real-resource-image/Pasted%20image%2020240524152545.png)

* Next Record offset
  * 현재 레코드에서 페이지 내 다음 레코드의 시작점까지의 상대적 오프셋

* Record Type
  * 레코드 유형으로 일반(0), 노드 포인터(1), 최소값(2), 최상위(3)의 4가지 값만 지원됨

* Order
  * 이 레코드가 힙에 삽입된 순서임
  * Infimum, supremum을 포함한 힙 레코드는 0번부터 번호가 매겨짐, Infimum은 항상 0, supremum은 항상 1임
  * 삽입된 사용자 레코드는 2부터 번호가 매겨짐

* Number of Records Owned
  * 페이지 디렉토리에서 현재 레코드가 '소유'한 레코드수
  * 향후 포스트에서 설명할 예정

* Info Flag
  * 레코드에 대한 boolean flag를 지정하는 4비트 비트맵
  * 현재 두개의 플래그만 정의도어 있음
    * min\_rec(1)는 이 레코드가 B+Tree의 non-leaf에서 최소 레코드임을 의미함
    * deleted(2)는 레코드가 삭제 표시되어 있으며, 향후 purge operation에 의해 실제로 삭제될 것임을 의미함

* Nullable field bitmap(optional)
  * 필드가 NULL인지 여부를 저장하기 위한 필드, nullable한 필드당 1비트를 사용하고, byte로 반올림됨
  * 필드가 NULL인 경우 해당 필드 값은 레코드의 키 또는 행부분에서 제거됨
  * Null이 필드가 없는 경우 이 비트맵은 존재하지 않음

* Variable file lengths array(optional)
  * 가변길이 필드당 8비트 또는 16비트 정수 배열(필드의 최대크기에 따라 다름)로 해당 필드에 대한 데이터 길이를 저장
  * 가변길이 필드가 없는경우, 이 배열은 없음

* record header는 row당 최소 5 byte이며, 가변길이 필드에 의해 더 길어질 수 있음

### Clustered indexes

![center](/image/real-resource-image/Pasted%20image%2020240524153758.png)

* Cluster Key Fields
  * 클러스 키 필드는 문자 그대로 함께 연결됨
  * InnoDB는 column유형 별 내부 저장소 형식의 raw byte를 단이 바이트 스트림으로 연결하기만 함
* Transaction ID
  * 이 레코드를 마지막으로 수정한 트랜잭션의 48비트 정수 트랜직션 ID
* Roll Pointer
  * 해당 레코드를 마지막으로 수정한 트랜잭션의 undu record의 rollback segment 위치를 포함하고 있는 구조체
  * 이 필드의 roll pointer 구조는 1비트의 "삽입중" 플래그, 7비트의 rollback segment ID, 4바이트 페이지 번호, 2바이트의 undo log위치의 페이지 오프셋으로 이루어짐
* Non-Key Fields
  * 기본키가 아닌 실제 행데이터가 단일 바이트 스트림으로 연결되어 있음

![center](/image/real-resource-image/Pasted%20image%2020240524160646.png)

* non-leaf 페이지의 레코드 포맷임
* non-leaf page는 MVCC가 아니기에, Transaction ID와 Roll Pointer filed는 없음
* non-key field 대신에 이 노드 포인터가 가르키는 하위 페이지 번호가 포함됨
* 클러스터 키는 NUL이 될 수 없으므로 nullable field bitmap도 없음

### Secondary indexes

* InnoDB의 Secondary Index는 clustered key와 전체 구조가 동일하지만, non-key대신 Primary Key Value(PKV)라고 하는 clustered key field를 포함함
* Secondary Index와 clustered key사이에 겹치는 필드가 있는 경우, Secondary Index레코드에 저장된 clustered key에서 겹치는 필드가 제거됨
* 예를들어, 테이블에 primary key(a,b,c)와 secondary Index(a,d)가 있는경우, 인덱스 내의 secondary key는 (a,d)가 되지만, PKV에는 (b,c)만 포함됨

![center](/image/real-resource-image/Pasted%20image%2020240524161849.png)

* sendary key fields는 clusterd key와 마찬가지로 단일 바이트 스트림으로 연결됨
* clustered key 필드는 정확한 동일한 방식으로 함께 연결되어 PKV를 만듬

![center](/image/real-resource-image/Pasted%20image%2020240524162016.png)

* Secondary index의 non-leaf page는 PKV가 레코드에 포함되며, 이는 레코드 값이 아닌, 레코드 키의 일부로 간주됨
* Secondary index는 고유하지 않을 수 있지만, 페이지 내의 각 레코드는 unique 식별자가 필요함
  * 그러므로 고유성을 보장하기 위해 PKV가 레코드에 포함되어 있어야함
* 즉, Secondary key의 non-leaf에 있는 레코드는 leaf 페이지의 레코드보다 4바이트 커짐

### row당 오버헤드

* 위의 그림을 보면 InnoDB에 필요한 행당 오버헤드를 쉽게 계산할 수 있음

* clusterd key leaf에는 헤더에 최소 5바이트, 트랜잭션 ID에 6파인트, 롤포인터에 7바이트, row당 총 18바이트가 필요함

* 매우 작은 테이블(2-3개의 정수컬럼을 가지는 테이블)의 경우 오버헤드가 상당히 높을 수 있음

* 또한 페이지당 오버헤드가 상당하여, 비 효율적으로 페이지를 채우면 많은 야의 공간을 차지할 수 있음

https://blog.jcole.us/2013/01/10/the-physical-structure-of-records-in-innodb/
