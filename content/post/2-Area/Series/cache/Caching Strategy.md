---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8510
tags:
  - Cache
  - hugo_blog
  - Caching-Strategy
categories: Cache
title: Caching Strategy
lastmod: 2024-04-21T09:36:36.344Z
---
## 어디에 저장할 것인가?

### In-memory caching

* 데이터를 데이터베이스나 디스크가 아닌 RAM에 저장하는 방식
* 높은 조회 성능을 가짐
* 비 휘발성, 서버가 종료되면 삭제됨
  * 중요한 데이터를 보관하기 힘듦
  * 정말 성능이 중요한 서버라면 RAM에 저장하고 분산 합의 알고리즘(ex. Raft 알고리즘)으로 분산해서 저장

### Distributed caching

* 하나의 네트워크에 있는 서버, 노드들의 데이터를 한곳에 캐싱하는 방식
  * ex) Redis, memcached
* 높은 가용성과 확정성이 필요한 서비스에서 유용함
* 다른 지역에있는 데이터베이스의 정보를 가져오면 시간이 오래 걸리기에 지역마다 caching server를 두어 지역간 지연시간을 줄임

### Client-Side caching

* 데이터를 사용자의 장비(ex. 웹브라우저)에 저장하는 방식
* javaScript, image같은 정적 리소스에 자주 접근해야하는 경우 유용함
* 최신의 데이터가 아닌 옛날 데이터를 가져오지 않을 수 있기에, 정책과 만료시간을 잘 고려해야함

## 캐시 전략

### Cache-Aside

![center|700](/image/real-resource-image/Pasted%20image%2020240216132133.png)

* 캐시에서 데이터를 확인 후, 캐시에 데이터가 없다면 데이터베이스에 요청하는 방식
* 어플리케이션이 캐시 관리의 책임을 가짐
* 캐시를 최신상태로 관리하도록 노력이 많이 필요함

> - 한번에 많은 캐시를 refresh할때 부하가 발생할 수 있음
>   * ex) 캐시서버가 재시작할떄, 모든 캐시를 12시에 초기화 할때
> - 이때 캐시서버에 많은 부하가 갈 수 있으므로 재시작시에는 어플리케이션에 바로 연결하는 것보다는 캐시를 warming후 연결하는것이 부하가 적음
> - 일정 시간에 초기화시 한번에 많은 부하가 갈 수 있으므로 정각에 모든 캐시를 초기화 하는것이 아닌, 무작위 텀을 두고 초기화 하는것이 부하가 적음

### Read-Through

![center|700](/image/real-resource-image/Pasted%20image%2020240216132233.png)

* 캐시에 데이터를 확인하고, 데이터가 없다면 데이터베이스를 읽어 캐시에 데이터를 작성하고 다시 캐시에서 데이터를 읽는 방식
* 어플리케이션에서는 캐시에서만 데이터를 읽음

### Write-Through

![center|700](/image/real-resource-image/Pasted%20image%2020240216132203.png)

* 쓰기작업시 캐시와 데이터베이스 두곳 모두에 데이터를 쓰는 방식
* 캐시에 항상 최신의 데이터가 있다는것을 보장
* 쓰기성능이 좋지 않음

> 비동기 처리로 쓰기성능을 향상시킬 수 있지 않을까?

### Write-Behind

![center|700](/image/real-resource-image/Pasted%20image%2020240216132217.png)

* 쓰기작업시 캐시에 데이터를 모아두었다가
* 쓰기성능이 증가됨
* 캐시를 제대로 관리하지 않으면 데이터 불일치가 발생할 수 있음

### Write-Around

![center|600](/image/real-resource-image/Pasted%20image%2020240218223235.png)

* 모든 데이터는 데이터베이스에 저장되고, 읽은 데이터만 캐시에 저장되는 방식
* Cache miss가 발생하는 경우에만 캐시에 저장되므로 데이터 불일치가 발생할 수 있음

## 캐시 성능 분석 요소

* hit rate
  * 요청에서 얼만큼 데이터가 적절히 반환되었는가 판단
* 데이터의 일관성
  * 데이터가 얼마나 일관되게 읽혀졌는지
  * 실데이터와 불일치가 얼마나 적게 발생했는지

### 참고자료

https://medium.com/@mmoshikoo/cache-strategies-996e91c80303\
https://yoongrammer.tistory.com/101

\#Cache\
\#Infrastructure
