---
date: 2024-08-12 23:17:42
updatedAt: 2024-09-05 10:31:51
tags:
  - MessagQueue
  - RabbitMQ
  - Infrastructure
  - hugo_blog
categories:
  - MessageQueue
title: AMQP 0-9-1 Model
lastmod: 2024-09-05T01:32:40.215Z
---
## AMQP 0-9-1

* 적합한 클라이언트 애플리케이션이 적합한 메시징 미들웨어 브로커와 통신할수 있도록 하는 메시징 프로토콜

* Message Broker는 publisher에게 메시지를 받아 consumer에게 라우팅함

* 네트워크 프로토콜 이므로 broker, publisher, consumer는 다른 시스템에 존재할 수 있음

* 메시지는 우체국이나 우편함에 비유되는 Exchange에 publish됨

* Exchange는 Binding이라는 규칙을 사용하여 메시지 사본을 대기열에 배포함

* broker는 Queue를 subscribe하는 consumer에게 메시지를 전달하건, consumer가 필요에따라 queue에서 메서지를 fetch/pull함

* 메시지를 publishing할때, publisher는 다양한 메시지 속성을 지정할 수 있음
  * 일부는 브로커에서 사용할 수 있지만, 나머지는 브로커가 볼 수 없으며, 메시지를 수신하는 애플리케이션에서만 사용됨
  * 네트워크가 불안정하고, 어플리케이션이 메시지를 처리할 수 없을 수 있으므로 AMQP 0-9-1 모델에는 message acknowledgements라는 개념이 있음
  * 메시지가 소비자에게 전달되면, 소비자는 자동 또는 개발자의 선택에 따라 큐에 알림
  * 메시지 확인이 사용중일때, 브로커는 해당 메시지 알림을 수신할때만 큐에서 해당 메시지를 완전히 제거함

* 메시지가 라우팅을 확장할 수 없는경우, 특정 상황에서는 메시지가 publisher에게 반환되거나, 삭제되거나 dead letter queue에 쌓일 수 있음

* queue, exchanges, binding은 AMQP엔티티임

* AMQP 엔티티는 broker 관리자 뿐만이 아니라, application에서 정의되어질 수 있기에 프로그래밍 가능한 protocol임

## Method

* HTTP메서드와 유사하게, AMQP는 몇개의 메서드가 구성되어 있음

* exchange.declare

* exchange.declare-ok

* exchange.delete

* exchange.delete-ok

* publisher나 consumer가 해당 메시지를 보내, exchange, queue, binding을 생성할 수 있음

https://www.rabbitmq.com/tutorials/amqp-concepts
