---
date: 2024-08-10 23:37:42
updatedAt: 2024-09-05 10:28:25
tags:
  - Infrastructure
  - hugo_blog
  - RabbitMQ
  - MessagQueue
categories:
  - MessageQueue
title: Subscriber
lastmod: 2024-09-05T01:32:51.214Z
---
## Consumer

* 에플리케이션이 메시지를 사용할 수 없다면, Queue에 메시지를 저장하는것은 불필요함
* AMQP 0-9-1 모델에는 Subscribe과 Polling이 있음
  * 애플리케이션이 메시지를 전달받도록 하는 Subscribe 방식 - 권장 옵션
  * 비효율적이며 대부분의 경우 피해야하는 Polling 방식
* Push API를 사용하면 애플리케이션이 특정 Queue에 Subscriber를 등록하거나, Queue에 가입해야함
* Queue에 두명 이상의 consumer를 등록하거나, exclude consumer를 등록할 수 있음
* 각 consumer는 consumer tag라는 문자열로된 식별자가 존재함
  * 이 식별자는 메시지 수신을 취소하는데 사용할 수 있음

### Rejecting Messages

* consumer 애피리케이션이 메시지를 받을 때, 메시지 처리가 성공 할 수도, 실패할 수도 있음
* 애플리케이션은 메시지를 거부하므로써 브로커에게 메시직 처리가 실패했음을 알릴 수 있음
* 메시지를 거부할 때 브로커에게 메시지를 폐기하거나, 다시 Queue에 넣도록 요청할 수 있음
* 대기열에 소비자가 한명 만 있는경우, 동일 소비자의 메시지를 거부했다가 다시 queue에 넣는 무한루프를 만들지 않도록 주의해야함

### Prefetching Message

* 여러 Consumer가 하나의 queue를 공유할때, 다음 ACK를 보내기전에 각 소비자가 한번에 처리할 수 있는 메시지 수를 지정하는것은 유영함
* 간단히 부하분산 기술로 사용하거나, 메시지가 일괄적으로 publish되는 경항이 있는경우 처리량 개선에 사용할 수 있음
* 예를들어, publisher특성상 매분 메시지를 전송하는 경우 유용함
* RabbitMQ는 채널 레벨 prefetch만 지원하고, 연결 또는 크기기반 prefetch는 지원하지 않음

### 기본 설정

* **Queue 바인딩:** 어떤 Queue의 메시지를 받을지 설정
* **Message Listener:** 메시지를 받아 처리하는 메소드 구현
* **Container Factory:** 메시지 컨테이너 생성을 위한 팩토리 설정

### 고급 설정

* **Concurrency:** 동시에 처리할 메시지 수
* **Error Handling:** 오류 처리 (Retry, Dead-Letter Exchange, Error Queue, Circuit Breaker)
* **Transaction:** 트랜잭션 관리
* **Acknowledgment:** 메시지 처리 확인
* **Prefetch Count:** 미리 가져올 메시지 수
* **Dead-Letter Exchange:** 처리 실패 메시지 보낼 DLX 설정
* **Message Converter:** 메시지 형식 변환

### 심화 설정 및 고려사항

* **메시지 처리 전략:** Pull vs Push, Batch 처리, Parallel 처리, Message Sequencing, Deduplication, Prioritization, Asynchronous Processing
* **오류 처리 심화:** Retry Strategies, Circuit Breaker Pattern, Bulkhead Pattern, Resilience4j
* **성능 최적화:** Batching, Compression, Caching, Profiling
* **확장성:** Horizontal Scaling, Sharding, Load Balancing
* **보안:** TLS/SSL, Access Control, Authentication, Authorization
* **모니터링:** Metrics, Logging, Alerting, Distributed Tracing
* **특수 기능:** Delayed Messages, Scheduled Messages, Message Routing
* **클라우드 환경:** Serverless Functions, Managed Messaging Services, Cloud-Native Patterns
* **테스트:** Unit Test, Integration Test, Chaos Engineering, Mutation Testing
* **패턴:** Observer Pattern, Strategy Pattern, Actor Model, CQRS

### 추가 키워드

* **Consumer Tag:** 소비자 식별
* **Channel:** 메시지 채널
* **Ack:** 메시지 처리 성공 확인
* **Nack:** 메시지 처리 실패 확인
* **Requeue:** 메시지 다시 큐에 넣기
