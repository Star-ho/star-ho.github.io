---
date: 2024-08-10 23:37:33
updatedAt: 2024-09-05 10:30:25
tags:
  - RabbitMQ
  - Infrastructure
  - MessagQueue
  - hugo_blog
categories:
  - MessageQueue
title: Publisher
lastmod: 2024-09-05T01:30:52.838Z
---
지금까지 다룬 Spring Boot Publisher 설정에 대한 핵심 키워드를 정리해 드리겠습니다.

### 핵심 설정

* **RabbitMQ 연결:** 호스트, 포트, 사용자명, 비밀번호 등 RabbitMQ 서버 연결 정보
* **Exchange:** Exchange 이름, 타입 (direct, fanout, topic, headers), 내구성, 자동 삭제 여부
* **Queue:** Queue 이름, 내구성, 독점 여부, 자동 삭제 여부, 인자 (DLX, TTL 등)
* **Binding:** Exchange와 Queue 바인딩, 라우팅 키
* **Template:** mandatory, returnCallback, confirmCallback 등
* **ConnectionFactory:** 연결 팩토리 설정 (커넥션 풀링, 재연결 등)
* **MessageConverter:** 메시지 변환기 설정 (JSON, XML 등)

### 추가 고려 사항

* **메시지 크기 및 속도:** 대용량 메시지 처리, 고속 메시지 전송
* **메시지 보장:** 확인, 트랜잭션, QoS
* **보안:** 인증, 암호화, 접근 제어
* **클러스터링:** 고가용성, 부하 분산
* **모니터링:** 메트릭, 로그
* **테스트:** 단위 테스트, 통합 테스트, Contract Test
* **비동기 처리:** AsyncRestTemplate, CompletableFuture
* **메시지 형식:** Custom Message Converter, Schema Registry, Type Conversion
* **확장성:** Dynamic Routing, Message Expiration, DLX
* **클라우드 환경:** Cloud-Native Messaging, Kubernetes
* **패턴 및 관례:** Builder Pattern, Template Method Pattern
* **Retry Mechanism:** 재시도 메커니즘
* **Circuit Breaker:** 회로 차단기
* **Idempotent Producer:** 중복 메시지 처리

### 심화 설정

* **메시지 형식:** Custom Message Converter, Schema Registry, Type Conversion
* **확장성:** Dynamic Routing, Message Expiration, DLX
* **클라우드 환경:** Cloud-Native Messaging, Kubernetes
* **테스트 및 모니터링:** Integration Test, Contract Test, Prometheus
* **보안 및 규정 준수:** TLS/SSL, IAM, Compliance
* **패턴 및 관례:** Builder Pattern, Template Method Pattern
* **Retry Mechanism:** 재시도 메커니즘
* **Circuit Breaker:** 회로 차단기
* **Idempotent Producer:** 중복 메시지 처리
* Confirm :

**각 키워드에 대한 자세한 설명은 이전 답변들을 참고하시거나, 궁금한 부분을 질문해주세요.**
