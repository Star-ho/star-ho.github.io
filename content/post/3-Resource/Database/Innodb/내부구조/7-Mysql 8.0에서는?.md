---
date: 2024-05-24 16:30:40
updatedAt: 2024-09-19 23:21:57
tags:
  - InnoDB
  - InnoDB-File-Structure
  - hugo_blog
  - Database
categories:
  - Database
title: 7-Mysql 8.0에서는?
lastmod: 2024-09-19T14:21:57.264Z
---
* Jeremy Cole의 InnoDB정보들은 MySQL 5버전에 관한 내용이며, 10년전의 내용임
* 현재 MySQL 8버전대를 사용하는데, 큰 기본 틀의 큰 차이는 없어 보이지만, 간단하게 알아볼 예정
  * Mysql버전에 따라 innodb버전이 업데이트됨
* Jeremy Cole의 Innodb\_ruby는 현재 8버전 대를 지원하고 있지 않음
* 필자는 아래 두가지 도구를 추천한다
  * https://github.com/alibaba/innodb-java-reader
    * 이 포스팅에서 사용할 도구
  * https://github.com/baotiao/inno\_space
    * 사용방법을 익히려 해보았지만, record를 파싱하는 부분에서 에러가 나서 결국 포기하였다.
    * 예제 데이터는 ibd2sdi를 이용하여 record정보를 가져오는것 같은데 실패하였다
      * 방법을 알면 댓글로 알려주시길 바랍니다 ㅠㅠ

## 무엇을 알아볼 것인가?

* space page구조
* page들은 doubly-linked list 구조인가?
* index record들은 singly-linked list구조인가?

## Space page 구조

![center](/image/real-resource-image/Pasted%20image%2020240524171103.png)

* 매우 작은 테이블의 구조를 나타냄
* 5버전대와의 차이점은 SDI페이지가 생겼다는 것이고, 이외에는 동일하다
  * SDI는 데이터베이스 객체에 대한 메타데이터를 저장하는 용도
  * 자세한 정보는 [링크](https://dev.mysql.com/doc/refman/8.0/en/serialized-dictionary-information.html)참고

## 여전히 page들은 doubly-linked list인가?

![](/image/real-resource-image/Pasted%20image%2020240524172057.png)

* 데이터가 들어있는 페이지의 일부이다
* page들이 prevPage, nextPage를 가지고 있고 INDEX페이지들은 앞 뒤 페이지를 참조하는 것을 알 수 있음
  * doubly-linked list구조 확인

## index record들은 singly-linked list구조인가?

![center](/image/real-resource-image/Pasted%20image%2020240524172847.png)

* 하나의 offset만 존재하며, 현재 primaryKeyPosition값에서 offset을 더하면, 다음 primaryKeyPosition이 되는 것을 알 수 있음
  * singly-linked list구조 확인
