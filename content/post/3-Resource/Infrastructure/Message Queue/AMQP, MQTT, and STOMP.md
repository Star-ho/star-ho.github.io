---
date: 2024-08-15 23:32:47
updatedAt: 2024-09-05 10:28:46
tags:
  - Infrastructure
  - RabbitMQ
  - MessagQueue
  - Protocol
  - hugo_blog
categories:
  - RabbitMQ
title: AMQP, MQTT, and STOMP
lastmod: 2024-09-05T01:29:28.388Z
---
## 웹에서 실시간 상효작용을 위한 접근법

* Polling
  * 브라우저에서 반복적으로 서버를 호출하여 새로운 데이터가 있는지 확인함
  * 효율적이지 못하며, 데이터 변경이 빈번하지 않을때 필요없는 요청이 너무 많이 발생함
* Server-Sent Envent(SSEs)
  * 폴링처럼 주기적으로 서버를 호출하는 대신, 개방형 연결(open connection)을 유지하여 서버가 데이터를 사용할 수 있을때, 클라이언트로 데이터를 전송함
  * 불필요한 여러 요청과 응답으로 인한 오버헤드를 줄일 수 있음
  * SSE의 주요단점은 단방향임
  * 서버는 데이터를 푸시할 수 있지만, 클라이언트는 동일한 연결을 통해 응답할 수 없기에, SSE는 일방적인 서버 업데이터에 이상적임
* WebSocket
  * 데이터를 반복적으로 요청하는 대신, 브라오저와 서버간에 통신라인을 열어둠
  * 이로인해 웹소켓은 양방향 통신을 가능하게함

## MQTT (Message Queuing Telemetry Transport)

* 배경
  * 과거에는 거대한 외딴 지역의 데이터를 모니터링을 하는데 어려움이 있었음
  * 불안정하고 대역폭이 낮은 네트워크, 모니터링 장치는 배터리로 작동하기 때문에 에너지절약이 필수적이 었음
  * 이로인해 지속적인 통신이나 높은 네트워크 사용은 실용적이지 않았음
* Light weight
  * MQTT는 간결한 메시지와 컴팩트한 페이로드를 사용하여 네트워크를 통해 전송해야 하는 데이터 양을 줄인 최소한의 프로토콜로 설계됨
  * pub/sub이라는 단 하나의 메시지 패턴에만 집중함
* QoS
  * QoS레벨이 존재, 메시지 게시 시 QoS수준을 선택할 수 있음
  * QoS 0은 대역폭 사용량이 낮지만, 전송 보장도 낮음
  * QoS 2는 가장 높은 대역폭 사용량과 가장 높은 전송 보장을 제공
* Last Will and Testament
  * 연결의 불안정한 특성을 고려할때, 예기치 않은 연결이 발생할때 브로커가 전송한 last will(마지막 유언)을 설정할 수 있음
  * 이 정보를 통해, subscriber는 유연한 대처가 가능함
  * 일반적으로 connection time에 클라이언트에 의해 지정됨
* Retained Message
  * MQTT브로커는 Topic에 대해 마지막으로 알려진 메시지를 유지하여, 다운타임후 온라인 상태가 된 모든 드바이스다 즉시 최신메시지를 받을수 있도록 보장함
* 위와 같은 특성에서 MQTT는 다양한 시나리오에서 사용가능했음
* 현재 IoT 사용시에 이상적인 선택임

## AMQP (Advanced Message Queuing Protocol)

* 모든 벤더가 구현할수 있는 프로토콜을 통해 서로 다른 시스템과 제품간의 상호 운영성을 보장한다는 비전을 바탕으로 탄생함
* Open Standard
  * 처음부터 개방형 표준으로 설계되었기에, 사양을 공개적으로 사용 가능함
  * 모든 벤더나 개발자는 이러한 사양을 사용하여 제품이나 시스템에 AMQP를 구현할 수 있음
* Reliable Message Deliveries
  * 신뢰성을 보장하는 주요 메커니즘 중 하나는 승인임
  * 메시지가 소비되면 브로커에게 확인을 전송하여 메시지가 처리되었음을 확인함
* Flexibility
  * 메시지가 먼저 Exchange로 이동한 다음 Queue로 이동함
  * Queue는 특정 패턴이나 기준에 따라 Exchange에 바인딩됨
  * 이러한 바인딩 메커니즘은 다양한 Exchange유형과 결함되어 복잡한 라우팅 로직을 가능하게함

## STOMP (Simple Text Oriented Messaging Protocol)

* 쉽게 구현하고 이해할 수 있는, 단순한 것을 만들기 위해 탄생함
* Simplicity
  * 미니멀리즘 방식으로 설계됨
  * 기능이 많은 프로토컬의 오버헤드와 복잡성 없이 메시징에 필요한 기본 기능만 제공
  * 아래와 같은 간단한 명령과 클래스가 존재함
    * Few Command
      * CONNECT, CONNECTED, SEND, SUBSCRIBE, UNSUBSCRIBE, BEGIN, COMMIT, ABORT, ACK, NACK, MESSAGE, CRECEIPT, ERROR
      * 풍부한 메서드 집합을 가진 AMQP와 대조됨
    * Connection Class
      * Start, Start-Ok, Secure, Secure-Ok, Tune, Tune-Ok, Open, Open-Ok, Close, Close-Ok
    * Channel Class
      * Open, Open-Ok, Flow, Flow-Ok, Close, Close-Ok
  * 적은 상태관리
    * STOMP는 상태저장기능이 있지만, AMQP는 세부적인 상태 관리 기능이 없음
    * AMQP는 Exchange, Queue, Binding등 다양한 속성을 지정하는 작업이 필요함
    * STOMP는 대상에게 메시지를 보내기만 하면 브로커가 세부사항을 처리함
  * Fewer Feature By Design
    * STOMP는 일반적으로 메시징의 기본을 잘처리하는 것을 목표로 하며, AMQP에 있는 많은 고급 기능은 없거나, 더 높은 수준에서 구현됨
    * 사용관점 뿐만 아니라 클라이언트 구현 관점에서도 STOMP를 단순하게 만듬
* Text-Oriented
  * 텍스트 기반이라 이해하고 디버깅하기 쉬움
  * 원시 STOMP프레임을 보면 HTTP처럼 사람이 읽을 . 수있음

https://www.cloudamqp.com/blog/rabbitmq-and-websockets-part-1-amqp-mqtt-stomp.html
