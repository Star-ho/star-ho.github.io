---
date: 2023-11-05T16:36:56
updatedAt: 2024-04-21 18:34:36+3250
tags:
  - Software-design
  - hugo_blog
categories:
  - Software-Design
title: Saga Pattern
lastmod: 2024-04-21T09:36:38.162Z
---
* 이전의 이름은 LLT(Long Lived Transaction)
* 이전의 이름에서 알수 있듯이 오래 살아있는 트랜잭션을 어떻게 처리할까는 문제의 해결책임
* 마이크로서비스화 되면서 서비스별 데이터베이스를 가지고 있음
* 그리고 각각의 서비스들이 트랜잭션을 가지고 있음
* 하나의 요청이 들어왔을때 각각의 서비스들에서 트랜잭션처리를 어떻게 관리할것인가?
  * A-B-C서비스를 거처야 하는 요청인데, 이에 관한 트랜잭션 처리를 어떻게 할지
  * ex) 롤백 처리를 어떻게 하는지?

## 정의

* SAGA는 로컬 트랜잭션의 연속임\
  ![Pasted image 20231105210948](/image/real-resource-image/Pasted%20image%2020231105210948.png)
* 각각의 로컬 트랜잭션은 트랜잭션 완료후 다음 트랜잭션에 메시지를 보내거나, 이벤트를 발생시킴
* 이 과정에서 에러 발생시 보상트랜잭션을 실행하여, 이전 로컬 트랜잭션 내용을 롤백시킴
* 이를 구현하는 두가지 방법이 있음
  * 아래의 예시는 e-커머스에서 주문을 넣는 예시로, 주문을 넣으면 고객의 잔고를 확인해서 잔고가 주문금액보다 많으면 잔고가 차감이  되며 주문이 승인되고, 적다면 주문이 거절됨
  * 주문서비스에서는 고객의 잔고를 알 수 없기에, 고객 서비스에 요청을 해야 주문의 승인여부를 확인할 수 있음

### Choreography-based saga

![Pasted image 20231105170101](/image/real-resource-image/Pasted%20image%2020231105170101.png)

* 서비스 흐름
  1. Order Service에서 `Post /orders`요청을 받고, PENDING 상태의 Order를 생성함
  2. Order 생성 이벤트를 발생
  3. Customer Service에서 잔고 차감을 시도
  4. 승인/거절 여부를 Order Service에 응답
  5. Order Service에서는 결과를 가지고 승인할지, 거절할지 결정
* 각각의 서비스가 로컬트랜잭션을 수행하고, 작업이 끝나면 다음 서비스로 메시지를 보내는 방식
* A -> B-> C 순서일때, A가 B를, B가 C를 호출함
* 롤백시 C->B->A 순으로 호출
* Orchestration방식보다 구현이 간편함(중앙 관리자가 없기 때문)

### Orchestration-based saga

![Pasted image 20231105170046](/image/real-resource-image/Pasted%20image%2020231105170046.png)

* 서비스 흐름
  1. order Service에서 `Post /orders`요청을 받고, Order saga orchestrator를 생성함
  2. saga orchestrator에서 PENDIN상태의 주문을 생성
  3. saga orchestrator에서 Customer Service에 잔고 차감명령을 보냄
  4. Customer Service에서 잔고 차감 명령을 시도함
  5. 그 후 orchestrator에 결과를 리턴함
  6. saga orchestrator에서 주문을 승인 or 거절
* Choreography방식보다 구현이 어려움(orchestrator가 추가되기 때문)
* 하지만 A->B->C상황보다 처리하기 쉬움(Orchestrator에서 A,B,C를 호출하면 되기 때문)
* 각 서비스가 다음에 호출해야할 서비스를 알 지 않아도 됨
* orchestrator가 단일 실패 지점이 될 수 있음

## 장점

* 애플리케이션이 분산 트랜잭션을 사용하지 않고도 여러 서비스에서 데이터 일관성을 유지 가능

## 단점

* 낮은 일관성
  *  송금을 예로 들면, 사용자 A의 계좌에서 돈이 인출되었지만 최종적으로 송금에 실패하는 중간 상태를 볼 수 있습니다.
* 보상 트랜잭션을 추가로구현해야 하기 때문에 개발 난이도가 높음
* 구조가 복잡함
* 주문한 고객이 결과를 바로 알수 없음
  1. saga가 완료되면 결과를 고객에게 응답
  2. saga가 시작할때 주문 ID를 포함한 응답으로 제공하고, client-side에서 결과를 주기적으로 폴링하여 결과 확인
  3. saga가 시작할때 주문 ID를 포함한 응답으로 제공하고, saga가 완료되면 webhook으로 고객에게 결과를 알려줌

> saga의 어원이 정확히 밝혀지지 않았음\
> \- 아래의 두가지 추측이 존재\
> \- 연결된 이벤트에 길고 자세한 이야기(문학에서 사용되는 용어)\
> \- Segregated Access of Global Atomicity의 약어

https://stackoverflow.com/questions/63319018/why-the-pattern-for-microservices-distributed-transactions-named-as-saga\
https://microservices.io/patterns/data/saga.html
