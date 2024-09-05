---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8620
tags:
  - Async-message
  - hugo_blog
title: Async-messaging
lastmod: 2024-04-21T09:36:36.368Z
---
# 비동기 메시징

* 결합도를 낮춰줌
  * 중간의 매개체를 통해 메시지를 전달하므로 수신자와 발신자가 서로를 몰라도 메시지 송/수신이 가능
  * publisher와 consumer가 서로를 알 필요가 없음
* consumer수를 늘림으로써 가용성 확보가 가능해짐
* consumer가 죽더라도 메시지 큐에 쌓이기 때문에 메시지 유실을 방지할 수 있음

# 방식

## Database(Transaction Outbox Pattern)

### 구성

* 데이터베이스 테이블을 messaging box로 이용
* 해당 데이터 + 상태 로 구성됨
  * 상태는 진행예정, 진행중, 완료 등의 상태가 있음
* 2번에서 outbox를 읽는 방법으로 polling이나 cdc를 사용하는 방법이 있음
  * polling시에는 relay서버가 해당 테이블을 주기적으로 확인
  * cdc를 사용한다면, cdc를 사용해 relay서버로 request보냄
* 응답성이 낮아도 되고, relay server 1개로 부하가 감당 가능할때 사용하기 좋음

### 장점

* 거의 모든 서비스가 데이터베이스를 사용하므로 추가 인프라가 필요 없음(비용절감)

### 단점

* 다른 방식에 비해 느림
  * cdc를 사용하던 polling을 사용하던 늘미
* relay서버 증설 시 부담이 있음
  * 한테이블을 여러 서버가 읽을때 락관리를 어떻게 할것인가
    * 깔끔하게 transaction level을 SERIALIZED로 올린다?
* 인스턴스가 2개 이상이면 lock을 걸어줘야함
  * 스케일링시 부담있음

## Redis Pub-sub

![center|700](/image/real-resource-image/Pasted%20image%2020240110132820.png)

* subscriber가 해당 channel을 구독하고 있을때, publisher가 데이터를 publish하면 구독하고 있는 모든 subscriber에게 데이터를 전달함
* subscriber가 존재하지 않는다면 따로 보관하지 않으므로 데이터는 사라짐
* 100프로 전송 보장이 되지 않아도 문제없는 케이스에서 사용하기 좋음
* 주로 채팅이나 푸쉬알림 등에 사용됨

### 장점

* 데이터를 따로 저장하지 않으므로 빠름

### 단점

* 컨슈머가 항상 해당 토픽을 확인하고 있어야하며 컨슈머가 없을시 데이터가 사라짐

## Message queue(메시지큐)

![center](/image/real-resource-image/Pasted%20image%2020240331144001.png)

* pull 방식과 push 방식이 존재
  * pull 방식은 컨슈머가 메시지큐에 요청을 보내 메시지를 가져와서 처리하는 방식
  * push방식은 메시지큐가 요청이 생겼을 때 컨슈머에게 메시지를 보내는 방식
* 메모리에 저장하는방식, 파일기반 저장방식, jdbc로 저장하는 방식이 있음
* kafka, rabbitMQ, ZeroMQ, Pulsar, SQS 등이 존재

### 참고자료

https://blog.bytebytego.com/p/why-do-we-need-a-message-queue\
https://blog.iron.io/message-queue-vs-streaming/\
https://dreamix.eu/insights/message-queue-vs-message-broker-whats-the-difference/\
https://www.baeldung.com/pub-sub-vs-message-queues\
https://www.linkedin.com/pulse/differences-between-message-queue-event-stream-frank-lieu\
https://risingwave.com/blog/differences-between-messaging-queues-and-streaming-a-deep-dive/\
https://www.cloudamqp.com/blog/why-is-a-database-not-the-right-tool-for-a-queue-based-system.html\
https://stackoverflow.com/questions/48099098/message-broker-vs-database-and-monitoring\
https://docs.oracle.com/cd/E19636-01/819-3567/mq\_service.html\
https://oliveyoung.tech/blog/2023-08-07/async-process-of-coupon-issuance-using-redis/BC
