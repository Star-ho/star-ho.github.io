---
date: 2023-11-05T22:21:43
updatedAt: 2024-04-21 18:34:36+3250
tags:
  - Async-message
  - hugo_blog
title: Transactional outbox
lastmod: 2024-04-21T09:36:38.162Z
---
## 문제 상황

* msa 환경에서 도메인 서버스로 보낸 create, update, delete 이벤트는 반드시 수행되어야함
* 하지만 2pc가 지원이 안되는 환경에서, 이벤트를 발행하기는 부담이 있음
  * 이벤트 발행후 롤백되는 문제
* 메시지가 순서가 보장되어야할 수도 있음

## 개념

* 메시지를 전송하는 서비스의 일부 비즈니스 엔티티를 저장한느 트랜잭션의 일부로 OUTBOX테이블에 데이터를 추가로 저장
* 이후 별도의 프로세스가 해당 메시지를 메세지 브로커에 전송\
  ![Pasted image 20231105224429](/image/real-resource-image/Pasted%20image%2020231105224429.png)
* Sender - 메시지를 전송하는 서비스
* Database - business entity와 message outbox를 저장하는 데이터베이스
* Message outbox - 관계형 데이터베이스에서는 하나의 테이블, NoSql 디비에서는 각각의 데이터베이스 레코드의 property(document or item)
* Message relay - outbox에 저장되어있는 메시지를 메시지 브로커에게 보내는 역할

## Message relay의 두가지 방법

### Transaction log tailing

![center|400](/image/real-resource-image/Pasted%20image%2020231105225014.png)

* 아래의 로그를 tailing하여 로그를 확인 하여 message broker에 전송
  * MySQL binlog
  * Postgres WAL
  * AWS DynamoDB table streams
* log tailing시 cdc를 고려
* kafka connect도 사용할 수 있다니 확인해 볼것

### Polling publisher

* outbox 데이터를 주기적으로 polling하여 변경 발생시 message broker에 이벤트 발행
* 주기적으로 db를 확인해야 하니 불필요한 자원 소모 발생

### 장점

* 모든 데이터가 outbox에 저장되므로 장애가 났을때 대응이 수월함
* 2PC가 지원이 안되는 환경에서 대안이 될 수 있음

### 단점

* 메시지를 직접 보내는 패턴보다 속도가 느릴 수 있음
  * 메시지는 필요한상황에 바로 보내기때문
  * 하지만 Transactional outbox는 중간에 Message Relay서버가 변경을 확인하고 서비스로 요청을 보낼때까지 시간이 빔
* Message Relay 서버가 추가됨으로서 관리해야 할게 하나 더 늘어남
* 한번 이상 명령이 수행될 수 있음
  * 그러므로 멱등성을 보장해야함
  * 작업마다 id를 기록해놓고 해당 id를 가직 작업이 수행되었으면 재처리 하지 않아야함

> 정보는 Transactional outbox로 저장하고 끝난후 id와 함께 api요청을 보내는건 어떨까?\
> consumer에서 id와 함께 api요청을 받으면 id로 데이터를 확인 후 처리

https://microservices.io/patterns/data/transactional-outbox.html\
https://microservices.io/patterns/data/transaction-log-tailing.html
