---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8520
tags:
  - Cache
  - Terms
  - hugo_blog
categories: Cache
title: Cache Passivation, Expiration, Eviction
lastmod: 2024-04-21T09:36:36.345Z
---
## Eviction

* 주기적으로 사용하지 않는 데이터를 삭제
* 주로 LRU(Least Recently Used)를 사용

## Expiration

* 캐시가 만료되는 시간

## Passivation

* 인메모리 캐시에서 지우기전, 다른 저장소(파일시스템, 데이터베이스)저장하는것

### 참고자료

<https://access.redhat.com/documentation/es-es/jboss_enterprise_application_platform_common_criteria_certification/5/html/jboss_cache_user_guide/cl.pass>

\#Cache
