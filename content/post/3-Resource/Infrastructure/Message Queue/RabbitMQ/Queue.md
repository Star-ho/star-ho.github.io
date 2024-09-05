---
date: 2024-08-15 20:34:36
updatedAt: 2024-09-05 10:29:54
tags:
  - RabbitMQ
  - Infrastructure
  - hugo_blog
  - MessagQueue
categories:
  - MessageQueue
title: Queue
lastmod: 2024-09-05T01:30:58.516Z
---
## Queue

* 애플리케이션에서 소비되는 메시지를 저장하는 장소
* Queue는 일부 속성을 Exchange와 공유하지만, 몇가지 추가 속성이 있음
  * Name
    * 256바이트의 UTF-8문자로 구성함
    * 빈 문자열을 전달시, AMQP  0-9-1 브로커는 고유한 대기열 이름을 생성함
    * amq. 로 시작하는 대기열은 브로커 내부용으로 예약되어 있음
      * 이 규칙을 위반하면 코드 403의 예외가 발생함
  * Durable(브로커 재시작 후에도 큐가 유지됨)
    * durable한지 transient한지 선언할 수 있음
    * durable하다면 Queue의 메타데이터는 디스크에 저장되고, transient하다면 가능한경우 메모리에 저장됨
      * publish된 메시지도 동일
  * Exclusive(하나의 연결에서만 사용되며, 해당 연결이 닫히면 큐가 삭제됨)
  * Auto-delete(큐에 연결된 모든 소비자가 연결을 끊으면 자동으로 삭제됨)
  * Arguemnt(optional, 메시지 TTL, 큐길이 제한과 같은 플러그인 및 브로커별 기능에서 사용)
* Queue를 사용하려면 Queue을 선언해야함
* Queue를 선언하면, Queue가 존재하지 않을경우 생성
  * Queue가 이미 존재하고 속성이 동일할경우 아무 영향을 끼치지 않음
  * 속성이 다른경우, 코드 406의 예외가 발생함
