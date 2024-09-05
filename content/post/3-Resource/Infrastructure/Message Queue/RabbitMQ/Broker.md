---
date: 2024-08-10 23:49:41
updatedAt: 2024-09-05 10:31:31
tags:
  - MessageQueue
  - RabbitMQ
  - Infrastructure
  - hugo_blog
categories:
  - MessageQueue
title: Broker
lastmod: 2024-09-05T01:31:47.941Z
---
## Broker 관련 설정: 메시지 브로커 설정 심층 분석

**Broker**는 메시지 중개자 역할을 수행하며, Producer와 Consumer 간의 메시지 전달을 책임지는 핵심적인 구성 요소입니다. Broker의 설정은 시스템의 성능, 안정성, 확장성에 직접적인 영향을 미치므로 신중하게 설정해야 합니다.

### 주요 Broker 설정 항목

* **Network 설정:**
  * **Binding:** 어떤 Queue에 어떤 Exchange를 바인딩할지 설정
  * **Virtual Host:** 논리적인 분리된 환경 구성
  * **Clustering:** 여러 Broker를 연결하여 클러스터 구성
* **Queue 설정:**
  * **Durability:** Queue의 지속성 설정 (메시지 유실 방지)
  * **Auto-delete:** 더 이상 사용되지 않는 Queue 자동 삭제
  * **Exclusive:** 단독 소비자만 접근 가능하게 설정
  * **Message TTL:** 메시지 유효 기간 설정
  * **Dead-Letter Exchange:** 처리 실패 메시지를 보낼 DLX 설정
* **Exchange 설정:**
  * **Type:** Direct, Fanout, Topic, Headers 등 Exchange 타입 설정
  * **Durability:** Exchange의 지속성 설정
  * **Auto-delete:** 더 이상 사용되지 않는 Exchange 자동 삭제
* **Message 설정:**
  * **Message Size:** 최대 메시지 크기 설정
  * **Message TTL:** 메시지 유효 기간 설정
* **Performance 설정:**
  * **Prefetch Count:** 소비자가 미리 가져올 메시지 수 설정
  * **Concurrency:** 동시에 처리할 메시지 수
* **Security 설정:**
  * **Authentication:** 인증 메커니즘 설정
  * **Authorization:** 권한 관리 설정
  * **SSL/TLS:** 보안 통신 설정
* **Monitoring 설정:**
  * **Metrics:** 메트릭 수집 (메시지 처리량, 지연 시간 등)
  * **Logging:** 로그 설정

### Broker 선택 시 고려 사항

* **기능:** 필요한 기능 (Durable, Transactional, Exactly-Once Delivery 등) 지원 여부
* **성능:** 처리량, 지연 시간 등 성능 요구사항 충족 여부
* **확장성:** 시스템 부하 증가에 따른 확장성
* **운영 편의성:** 관리 도구, 모니터링 기능 등
* **커뮤니티:** 활발한 커뮤니티 지원 여부

### 주요 메시지 브로커 종류 및 특징

* **RabbitMQ:** 다양한 기능, 높은 확장성, 활발한 커뮤니티
* **Kafka:** 고성능, 분산 시스템, 스트리밍 처리에 적합
* **ActiveMQ:** 다양한 프로토콜 지원, 안정성
* **Amazon SQS:** 클라우드 기반, 쉽고 빠른 설정
* **Google Pub/Sub:** 클라우드 기반, 스케일링 용이
