---
date: 2024-05-19 22:44:51
updatedAt: 2024-09-19 23:21:46
tags:
  - InnoDB
  - InnoDB-File-Structure
  - hugo_blog
  - Database
categories:
  - Database
title: 3-InnoDB Index
lastmod: 2024-09-19T14:21:46.602Z
---
## Index

* 물리적인 인덱스 구조를 알기전, InnoDB에서 Index에 대해 중요하게 알아야하는 아래 3가지에 대해 알아야 함

1. 모든 테이블은 primary key를 가지고 있다
   * 테이블 생성시 primary key를 설정하지 않는다면, 먼저 non-NULL unique키를 사용하고, 없다면 48bit의 숨겨진  ROW id를 primary key로 사용함
2. row data(primary key가 아닌 필드)는 primary key 인덱스 구조 내에 저장됨
   * 이를 clustered key라고 명함
   * 인덱스 구조는 primary key필드에 키가 저장되며 row data는 해당 키에 연결된 값임(MVCC의 경우 추가적인 필드가 포함됨).
3. Secondary key는 동일한 인덱스 구조에 저장되며, 키는 해당하는 Secondary key이며, 값은 primary임

## Index page 구조

![center](/image/real-resource-image/Pasted%20image%2020240519225801.png)

* FIL header and trailer
  * 모든 페이지 유형에 일반적이고 공통적임
  * 다른 페이지 유형들과 다른점은, previous page와 next page 포인터가 인덱스 키를 기준으로 동일한 수준의 이전페이지와 다음페이지를 오름차순으로 가르킴
  * 이로인해 모든 페이지가 이중으로 연결된 double-linked list가 형성됨
* FSEG header
  * 이전 글에서 보았던, index root page의 FSEG헤더에는 포함됨
    * 해당 인덱스에서 사용하는 파일 세그먼트에 대한 포인터가 포함되어 있음
  * 다른 index page는 FSEG header를 사용되지 않고 0으로 채워짐
* INDEX header
  * Index 페이지 관리와 record관리에 필요한 필드가 포함되어 있음
  * 자세한 설명은 아래에 있음
* System records
  * 인덱스의 각 페이지에는 infimum과 supremum로 불리는 system record가 존재함
  * 이러한 레코드들은 페이지의 고정된 장소에 저장되어, 패이지 내에서 바로 접근이 가능함
* User records
  * 실제 데이터임
  * 각 레코드는 가변길이의 헤더와, 실제 데이터 컬럼을 가지고 있음
  * 헤더에는 오름차순으로 정렬된 singly-linked list를 구현하기 위한 next record 포인터를 포함함
  * 자세한 설명은 아래에 있음
* The page directory
  * FIL 트레일러에서 시작하여 페이지의 top에서 아래쪽으로 커지며(메모리 스택과 유사한 구조라고 생각됨), 페이지의 일부 레코드(4~8번째 레코드마다)에 대한 포인터를 포함함

## INDEX Header

![center](/image/real-resource-image/Pasted%20image%2020240519231114.png)

* Index ID
  * 해당 페이지에 속해있는 index의 ID를 의미함
* Format Flag
  * 해당 페이지 안에있는 record들의 포맷을 의미함
    * Number of Heap Record필드의 상위비트(0x8000)비트에 저장됨
  * 현재 COMPACT와 REDUNDANT가 가능함
    * 뒤에 자세히 설명함
* Number of Heap Records
  * infimum과 supremum 시스템 레코드, 삭제된garbage records를 포함한 페이지 내의 총 레코드 수를 의미함
* Heap Top Position
  * 현재 사용된 공간의 마지막 바이트 오프셋을 가르킴
  * heap 상단과 page directory의 마지막의 모든 공간은 여유공간임
* Garbage Space
  * garbage 레코드안에 있는 삭제된 레코드가 소비한 총 바이트 수를 저장함
* Last Insert Position
  * 페이지 내의 마지막으로 추가된 레코드의 바이트 오프셋을 저장함
* Page Direction
  * 현재 LEFT, RIGHT, NO\_DIRECTION 세가지 값이 사용됨
  * 페이지가 순차적으로 insert되는지, 무작위로 insert되는지를 나타냄
  * 각 insert시 마지막 insert위치의 레코드를 일고, 해당 키를 insert된 레코드 키와 비교하여 insert방향을 결정함
* Number of Inserts in Page Direction
  * 한번 페이지 방향이 설정된 후 방향을 바꾸지 않은 모든 다음 삽입은 이값을 증가시킴
    * 한번 페이지 방향이 설정된 후에 같은 방향으로 insert된 레코들 수를 의미
* Number of Directory Slots
  * 페이지 디렉토리의 크기(16바이트 오프셋인 slot의 단위)
* Page Level
  * index에서 해당 페이지의 level을 의미
  * Leaf 페이의 레벨은 0이고, B+트리를 따라 올라감
  * 일반적으로 3레벨 B+트리에서 루트는 레벨2, internal non-leaf page중 일부가 level 1, leaf page는 level 0임
  * 항후 게시글에서 추가로 설명함

## Record format: REDUNDANT vs COMPACT

* COMPACT 레코드 형식은, Barracuda테이블의 새로운 형식이고, REDUNDANT레코드 형식은 Antelope테이블의 기존 형식임
  * 둘다 Barracuda가 만들어질때까지, 공식적인 이름이 없었음
* COMPACT형식은 각 레코드에 중복 저장되어 data dictionary에 있는 정보(필드수, nullable한 필드, 동적 길이 필드)를 대부부분 제거함

## record pointer에 대한 추가설명

* record pointer는 INDEX Header안에 있는 Last Insert Position field, System record와 user record의 next pointer, page directory안에 있는 모든값 등 여러 다른 위치에서 사용됨
* 모든 레코드는 헤더(가변 길이 일수 있는)와 실제 레코드 데이터(가변 길이 일수 있는)를 포함함
* record pointer는 레코드 데이터의 첫번째 바이트 위치를 가르키며, 실제로는 header와 record 데이터 사이에 위치함
* 이를 통해 역방향으로 헤더를 읽고, 정방향으로 레코드 데이터를 읽음
* 시스템 및 사용자 필드에서 next record pointer를 항상 레코드의 첫번째 필드임
  * 이로인해, 항상 가변 width레코드 데이터를 파싱할 필요없이 페이지의 모든 레코드를 매우 효율적으로 읽을 수 있음

## System records: infimum and supremum

* 모든 INDEX 페이지는 고정된 위치에 infimum과 supremum이라는 시스템 레코드가 포함함\
  ![center](/image/real-resource-image/Pasted%20image%2020240520221729.png)
* 두 시스템 레코드는 일반적인 헤더를 가지며, infimum과 supremum이 유일한 데이터임
  * 데이터를 가지고있는 필드 바로 앞은 다음 레코드 포인터임

### infimum record

* infimum record는 페이지에서 모든 키보다 낮은 값을 나타냄
* "next record"포인터는 가장 낮은 키를 가진 사용자 레코드를 가르킴
* infimum은 사용자 레코드를 순차적으로 스캔하기 위한 고정된 진입점 역할을 함

### supremum

* supremum레코드는 페이지에서 가장 높은 키를 나타냄
* "next record"포인터는 항상 0임
  * 페이지 헤도로 인해 실제 레코드에 대해서는 항상 NUMM을 나타내며, 위효화지 않은 위치임
* 페이지에서 가장 높은 키를 가진 사용자레코드의 "next record"포인터는 항상 supremum레코드를 가르킴

## User records

* user record의 길고 복잡하기에 자세한 설명은 다음 게시글에서 다룸

* 사용자 레코드는 삽입된 순서대로 page body에 추가되며, 각 레코드 헤더의 "next record"포인터를 사용하여 키별로 오름차순으로 단일 링크됨
  * 이전에 삭제된 리코드에서 기존 여유공간을 차지 가능

* 단일 링크된 목록은 infimum record부터 오름차순으로 모든 사용자 레코드를 거쳐 supremum으로 끝남

* 이 list를 사용하면 한페이지의 모든 사용자 레코드를 오름차순으로 스캔하는 것은 간단함

* 또한 FIL헤더의 "next page"포인터를 사용하면 전체 인덱스를 오름차순으로 한페이지에서 다른 페이지로 쉽게 스캔 가능
  * FIL헤더로 인해 오름차순 테이블 스캔도 간단히 구현 가능

1. 인덱스 안의 첫번째 page에서 시작
   * 이 페이지는 B+트리 탐색을 통해 찾을 수 있음(향후 게시글에서 설명)
2. infimum record를 읽고, next record 포인터를 따라감
3. record가 supremum이라면 step 5로 이동하고, 아니라면 레코드 내용을 읽고 처리함
4. "next record"포인터를 따라가 step 3으로 돌아감
5. "next page" 포인터가 NULL을 가르키면 종료하고 아니라면 "next page"포인터를 따라 step 2로 돌아감

\*레코드가 doubly-linked가 아닌 singly-linked이므로 내림차순 탐색은 쉽지 않음

## Page directory

* page directory는 FIL trailer에서 시작하여 user record방향으로 커짐
* page directory는 4-8개의 레코드 포인터를 가지고 있으며, infimum과 supremum값을 포함함

![center](/image/real-resource-image/Pasted%20image%2020240520223401.png)

* page directory는 단순히 페이지 레코드에 대한 16비트 오프센 포인터인 동적 크기 배열(dynamically-sized array)임
* 향후 게시물에 자세하게 설명할 예정

## Free space

* user record(위쪽으로 증가함)와 page directory(아래로 증가함) 사이의 공간을 free space로 간주함
* 두 섹션이 중간에 만나서 여유공간이 소진되면 페이지가 가득 찬것으로 간주함
  * garbage를 제거하기 위한 re-organizing으로 공간을 확보할 수 없을때도 가득 찬것으로 간주함

https://blog.jcole.us/2013/01/07/the-physical-structure-of-innodb-index-pages/
