---
date: 2024-08-15 20:35:20
updatedAt: 2024-09-05 10:31:04
tags:
  - MessageQueue
  - RabbitMQ
  - hugo_blog
  - Infrastructure
categories:
  - MessageQueue
title: Exchange and binding
lastmod: 2024-09-05T01:31:29.390Z
---
## Exchange

* 다양한 속성을 설정할 수 있음
* 가장 중요한 옵션은 name, durablity, auto-delete(마지막 대기열이 해해제되면 자동 삭제됨), argument 임

### Default Exchange

* 브로커가 미리 선언한 이름이 없는 direct exchange
* 간단한 애플리케이션에 매우 유용한 한가지 속성이 있는데, 생성되는 모든 큐는 큐 이름과 동일한 라우팅 키를 사용하여 자동으로 바인딩됨
* 예를 들어 "search-indexing-online"이름의 큐를 선언하는  브로커는 "search-indexing-online"를 사용하여 기본 exchange에 해당 큐를 바인딩함

### Direct Exchange

![|center|700](/image/real-resource-image/Pasted%20image%2020240812233351.png)

* 메시지 라우팅 키를 기반으로 메시지를 대기열에 전달함
* 메시지의 유니캐스트 라우팅에 이상적이지만, 멀티캐스트 라우팅에도 사용 가능함
* 라우팅 키 K를 가진 queue가 라우팅 R을 가진 새 메시지가 direct exchange에 도착하면 교환은 K=R인경우 해당 대기열로 라우팅함
* 동일한 라우팅 키 K를 가진 direct exchange가 여러개 있으면, Exchange는 K=R인 모든 대기열에 라우팅함

### Fanout Exchange

![|center|700](/image/real-resource-image/Pasted%20image%2020240812233932.png)

* 바인딩된 모든 대기열로 메시지를 라우팅하며, 라우팅 키는 무시됨

* N개의 queue가 Fanout Exchange에 바인딩 되어 있는경우, 새 메시지가 Exchange에 publish되면, 메시지의 사본이 모든 N개의 대기열에 전달됨

* 브로드 캐스트 라우팅에 이상적임

* Fanout Exchange는 바인딩된 모든 queue에 메시지 사본을 전달하므로 사용 사례는 다들 비슷함
  * 대규모 멀티 온라인 게임에서 순위표 업데이트
  * 스포츠 뉴스 사이트에서 실시간으로 모바일 클라이언트에 점수 업데이트를 하는데 사용할 수 있음
  * 분산 시스템에서 다양한 상태 및 구성 업데이트에 사용될 수 있음

### Topic Exchange

* 라우팅 키와 대기열을 exchange에 바인딩하는데 사용된 패턴의 일치 여부에 따라 메시지를 하나 또는 여러개의 대기열로 라우팅함
* 다양한 pub/sub패턴을 구현하는데 자주 사용됨
* 일반적으로 멀티 캐스트 라우팅에 사용됨
* 수신할 메시지 유형을 선택적으로 선택하는 여러 소비자/애플리케이션에 관련된 문제가 있을 떄마다 고려할 필요가 있음

### Headers Exchange

* 라우팅 키 보다는 메시지 헤더로 더 쉽게 표현되는 여러 속성에 대한 라우팅을 위해 설계됨
* Headers Exchange는 라우팅 키속성을 무시함
* 라우팅에 사용되는 속성을 헤더에서 가져옴
* 헤더의 값이 바인딩시 지정된 값과 같으면 바인딩함
* 일치하는 헤더를 2개이상 사용하여 대기열을 헤더 교환에 바인딩 할 수 있음
* 브로커는 애플리케이션으로 부터 모두 일치 또는 일부만 일치하는 메시지를 고려햐기 위해 x-match 인수가 사용됨
  * x-match가 all이면 모든 값이 일치해야함
  * x-match가 any면 하나라도 일치하면 됨
  * x-match를 any-with-x또는 all-with-x로 설정하면 문자열 x-로 시작하는 헤더도 일치항목을 평가하는데 사용됨

## Binding

* Exchange에서 메시지를 Queue로 라우팅 하기 위해 사용하는 규칙

* 일부 Exchange 유형해서 사용하는 라우팅 키 속성이 있을 수 있고, Exchange E가 메시지를 Queue Q로 라우팅하도록 지시하려면, Q를 E에 바인딩해야함

* 라우팅 키의 목적은 Exchange에 publish된 특정 메시지를 바인딩된 Queue로 라우팅하도록 선택하는것

* 라우팅 키는 필터와 같은 역할을함

* 이러한 방향지정 계층이 있으면 publishing을 직접 사용하여 구현하기 불가능하거나 매우 어려운 시나리오에 대해 Queue를 라우팅 할 수 있으면 중복작업이 줄어듬

* 메시지가 어떤 Queue로도 라우팅 할 수 없는경우, publisher가 설정한 메시지 특성에 따라 메시지가 삭제되거나, publisher에게 되돌아감
