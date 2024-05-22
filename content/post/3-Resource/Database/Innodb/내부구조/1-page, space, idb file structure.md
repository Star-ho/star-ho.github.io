---
date: 2024-05-10 22:40:29
updatedAt: 2024-05-22 23:07:03
tags:
  - "#InnoDB"
  - hugo_blog
  - InnoDB-File-Structure
categories:
  - Database
title: 1-page, space, idb file structure
lastmod: 2024-05-22T14:07:03.878Z
---
## Page

* 각 스페이스는 16KiB의 페이지로 나누어짐
  * UNIV\_PAGE\_SIZE를 변경하거나, InnoDB 압축을 사용하는경우 변경 가능
* 오프셋 이라고하는 32bit 정수 페이지 번호가 할당됨
* 파일의 시작부분부터 해당 페이지 위치까지의 실제 오프셋임
* 페이지 0은 파일 오프셋 0, 페이지1은 오프셋은 16384(16\*1024)임

### 페이지 구조

![center](/image/real-resource-image/Pasted%20image%2020240511112606.png)

* 각 페이지는 38바이트의 FIL(file의 줄임말) header와 FIL Trailer를 가지고 있음
* 헤더에는 페이지의 남은 부분을 결정하는 page type 필드를 포함하고 있음

### FIL Header / Trailer

![center](/image/real-resource-image/Pasted%20image%2020240511112813.png)

* page type은 페이지의 다른 부분을 파싱하기 위해 필수적인 부분임
  * page type은 file space management, extent management, the transaction system, the data dictionary, undo logs, blobs, indexes가 할당될 수 있음
  * 페이지는 앞서 나열한 페이지가 될 수 있다는것을 의미함
* 페이지가 초기화 될때, page number(Offset)이 할당됨
  * 해당 필들드에서 읽은 페이지번호가 파일에 오프셋을 기준으로 해야하는 페이지 번호가 일치하는지 확인하여, 읽기가 올바른지 확인함
  * 이 필드가 초기화되면 페이자가 초기화되었다는 것을 의미함
* 해당 페이지의 유형과 같은 이전 페이지와 다음페이지 포인터가 헤더가 저장되어 있음
  * 이 필드들로 페이지를 double-linked list 구조가 됨
  * 인덱스 페이지를 동일한 수순으로 연결하는데 사용되므로 전체 인덱스 스캔을 효율적으로 수행할 수 있음
  * 대부분의 페이지 유형은 이 필드들을 사용하지 않음

## Space - tablespace

* 각 테이블당 하나의 space가 존재
* 하나의 스페이스는 .idb파일로 구성됨
  * 하나 이상의 idb파일로 구현 될 수 있음
  * 물리적으로 하나 이상의 파일이지만, 논리적으로 단일파일 취급함
* .idb파일은 하나의 테이블에 대한 정보가 들어감
  * 하나의 .idb파일에 여러 테이블이 들어갈 수 있는 구조지만, 하나의 테이블만 넣음
* space별로 32bit 정수인 space ID가 할당됨
  * 해당 space를 참조할 목적
* InnoDB는 InnoDB에서 필요로 하는 장부(book-keeping)를 작성하기 위해 system space를 가지고 있음
  * system space의 space ID는 0임
* 많은 페이지가 연결된것
* 효율적으로 페이지를 관리하기 위헤 1MiB 블록 단위로 페이지를 관리함
  * 이를 extent라 부름
* 파티션을 나누면 각각의 space가 나누어짐

### Space File 구조

![center](/image/real-resource-image/Pasted%20image%2020240511225238.png)

* Innodb는 모든 page, extent, space를 추적-관리하기 해야하므로 위와같은 구조를 가짐
* space의 첫번째 페이지(page 0)은 항상 FSP\_HDR 또는 file space header 페이지임
* FSP\_HDR페이지는 space의 크기, free, fragmented, full extents의 목록을 추적할 수 있는 FSP header 구조를 포함하고 있음
* FSP\_HDR페이지는 내부적으로 256 extents(또는 16384페이지, 256MiB)에 대한 정보를 저장 가능함
  * 그러므로 256extents마다 XDES정보를 저장하기 위해 공간을 미리 예약해놓음
* XDES와 FSP\_HDR페이지는 XDES에서 FSP해더 구조가 0으로 처리된다는 점을 제외하면 동일함
* 추가페이지는 스페이스 파일이 커짐에 따라 할당됨
* 두번째페이지인 IBUF\_BITMAP페이지는 insert buffering과 관련된 정보를 저장함(이 글의 범위를 벗어나는 부분이라 더 이상 언급하지 않음)
* 세번째 페이지인 INODE페이지는 파일 세그먼트와 관련된 목록을 저장하는데 사용됨
  * 각 INODE페이지에는 85개의 INODE항목을 저장할 수 있으며, 각 인덱스에는 2개의 INODE항목이 필요함

### System space

* InnoDB에는 space ID가 0인 system space가 존재함
* InnoDB의 작동에 중요한 정보를 저장하기 위해 고정 페이지 번호로 할당된 페이지가 포함되어 있음
* 다른공간과 마찬가지로 FSP\_HDR, IBUF\_BITMAP, INODE페이지가 처음 3페이지로 할당되어 있음

![center](/image/real-resource-image/Pasted%20image%2020240511231613.png)

* 3번째 SYS페이지에는 insert buffering에 관련된 헤더와 부기정보가 있음
* 4번째 INDEX페이지에는 insert buffering에 관련된 index구조의 루트 페이지가 존재
* 5번째 TRX\_SYS페이지에는 InnodDB의 트랜잭션 시스템의 동작과 관련된 정보가 존재함
  * 최신 transaction ID, Mysql binary log정보, double write buffer extents의 위치 등
* 6번째 SYS페이지에는 첫번째 롤백 segment page정보가 있음
  * rollback segment data의 저장이 필요하다면, 추가적인 페이지가 할당될 수 있음
* 7번째 SYS페이지에는 data dictionary와 관련된 헤더가 있음
  * data dictionary를 구성하는 인덱스의 루트 페이지 번호를 포함함
  * 루트 페이지 번호가 data dictionary 자체에 저장되므로,다른 인덱스를 찾기 위해 필요함
* 64-127번째 페이지에는 double write buffer에 저장됨
* 128-192번째 페이지에는 두번째 double write buffer

## IBD 파일 구조

![center](/image/real-resource-image/Pasted%20image%2020240511233507.png)

* 런타임에 인덱스를 생성하는 fast index creation을 제외하고, 필수 초기 3페이지 이후 공간에 할당된 페이지들은 테이블 생성에 정의된 순서대로 테이블에 있는 각 인덱스 루트 페이지가 됨
* 3번째 페이지는 clustered index의 루트페이지, 4번째 페이지는 첫번째 secondary index의 루트페이지임
* InnoDB의 부기 구조는 대부분 시스템 공간에 저장되므로, 테이블별 공간에 할당된 대부분의 페이지는 INDEX유형이며, 테이블 데이터를 저장함

참고

https://blog.jcole.us/2013/01/03/the-basics-of-innodb-space-file-layout/
