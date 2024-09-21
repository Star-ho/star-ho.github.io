---
date: 2024-03-04T22:41:07
updatedAt: 2024-09-21 23:54:23
tags:
  - Server-History
  - hugo_blog
  - Reactive
  - Server
categories:
  - Server-History
title: 7-Reactive Manifesto
lastmod: 2024-09-21T14:54:23.248Z
---
* 2013년 어플리케이션의 요구사항(짧은 응답시간, 100%가용성)의 변화로 리액티브 시스템을 정의한 리액티브 선언문(reactive manifesto)가 작성됨 (현재 최신 버전 2014년 v2.0)
* 리액티브 선언문에서는 리액티브 시스템이란 Responsive, Resilient, Elastic, Message Driven을 가진 시스템이라고 정의
* 이중 jvm을 타겟으로한 비동기, 논블로킹의 표준인 reactive streams나왔고 이를 구현한 Project Reactor와 rxJava가 나왔다.
* 이중 Project Reactor와 Spring Framework가 손잡고 나온것이 Spring Webflux이다.
* 현재 Spring Webflux에서 많이 사용하는 Http Server인 Reactor Netty는 Project Reactor의 프로젝트중 하나이다

![Pasted image 20231214232659](/image/real-resource-image/Pasted%20image%2020231214232659.png)

### Responsive(응답성)

* 시스템은 가능한 적시에 응답해야함
* 사용성과 유용성 때문에 중요한것도 맞지만, 문제를 신속하게 감지할수 있다는 더 중요한 장점이 있음
* 응답성이 높은 시스템은 신속하고 일관된 응답을 주는것에 중점에 두며, ***신뢰할 수 있는 상한선***을 두어 일관된 서비스 품질을 제공함
* 위와같은 일관된 행동은 오류를 간소화하고, 최종 사용자와의 신뢰를 구축하며, 더 많은 상호작용을 장려함

### Resilient(회복성)

* 시스템은 장애가 발생하여도 응답성을 유지해야함
* 고 가용성의 중요한 시스템 뿐만아니라, 중요하지 않은 시스템도 포함됨
* 회복성은 복제, 격리, 위임, 고립를 통해 달성됨
* 구성요소에 장애 발생시, 장애가 발생한 구성요소를 격리하여 전체 시스템에 영향을 주지않으며, 시스템 일부만 장애발생 및 복구가 가능하게함
* 장애가 발생한 시스템에 대한 복구는 다른 구성요소에게 위임되며, 필요한 경우 복제를 통해 고가용성을 달성함
* 구성요소의 클라이언트는 장애를 복구하는데 부담을 덜 느낄수 있음

### Elastic(탄력성)

* 다양한 작업량(갑자기 요청이 많아지는 경우)에도 응답성이 유지되어야함
* reactive system은 이러한 다양한 요청량을 처리하기 위해 자원을 늘리거나 줄이는 것으로 대응할 수 있음
* 컴포넌트를 샤딩이나 복제하고, 이들에게 입력을 분산시키는 기능을 제공하는, 경합지점이나 병목이 없는 설계를 의미함
* reactive system은 실시간 성능 측정 기능을 통해 예측 및 반응형 확장 알고리즘을 제공함
* 이 시스템은 상용 하드웨어 및 소프트웨어 플랫폼에서 비용 효율적인 측면에서 탄력성을 달성함

> aws에서 툭하면 elastic이 나오게 되는데 선언문에 나온 elasitc의 개념이다.[링크](https://wa.aws.amazon.com/wellarchitected/2020-07-02T19-33-23/wat.concept.elasticity.en.html)\
> 리액티브 선언문을 많이 읽어보며, 어떻게 개발해야 할지 개발 방향에 좀 감을 잡은거 같다.

### Message Driven(메시지 전달)

* reactive system은 비동기 메세지 전달에 의존하여 느슨한 결함, 격리 및 위치 투명성을 보장하는 구성요소간에 경계를 설정함
* 이 메세지는 실패를 메시지로 위임할 수 있는 수단도 제공함
* 명시적 메시지 전달을 사용하면 시스템에서 대기열을 형성 및 모니터링 하고, 필요시 배압을 사용하여 부하관리 및 탄력성, 흐름제어가 가능함
* 위치 투명 메시징을 사용하면 클러스터 전체, 혹은 단일 호스트 내에서 동일한 구조로 장애를 관리할 수 있음
* 비차단 통신을 통해 수신자는 활성상태일 때만 리소스를 사용하여 시스템의 오버헤드가 줄어듬

<https://www.reactivemanifesto.org/ko>\
<https://www.reactive-streams.org/>\
<https://github.com/reactor/reactor>

\#Reactive\
\#Concept
