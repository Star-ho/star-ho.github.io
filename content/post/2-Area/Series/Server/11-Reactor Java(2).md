---
date: 2024-03-05T22:41:02
updatedAt: 2024-09-21 23:54:41
tags:
  - Server-History
  - hugo_blog
  - Reactive
  - Server
categories:
  - Server-History
title: 11-Reactor Java(2)
lastmod: 2024-09-21T14:54:41.259Z
---
# Threading and Scheduler

* Flux나 Mono를 얻는다고 실행되지 않음
* 따로 지정하지 않으면 Reactor는 subscribe가 발생한 쓰레드에서 모든 연산자가 실행됨
* Reactor가 실행되는 위치는 스케줄러에 의해 정해짐

### Schedulers.immediate()

* 직접적으로 현재 실행되고 있는 쓰레드에서 실행됨

### Schedulers.single()

* 쓰레드를 생성하여 스케줄러가 dispose될때까지 모든 호출자에 대해 동일한 쓰레드를 재사용
* 호출별 새로운 쓰레드를 생성하고 싶다면 Schedulers.newSingle()을 사용해야함

### Schedulers.elastic()

* 배압 문제를 숨기고 너무많은 쓰레드를 생성하기에 Schedulers.boundedElastic()가 도입된 이후로 잘 사용되지 않음

### Schedulers.boundedElastic()

* 필요에 따라 워커풀을 생성하고, idle한 워커풀이 있다면 재사용함
* 워커풀이 일정시간 사용되지 않으면 삭제됨(기본 60초)
* Schedulers.elastic()과 달리 워커풀 생성에 제한이 있음(기본 cpu core\*\10)
* 한도에 도달한다면 최대 100,000 작업이 큐에 추가됨
* 쓰레드가 다시 재사용 될때 큐에 추가됨
  * 100,000이 넘게 추가되면 에러발생
* 블로킹프로세스에 자체 쓰레드를 부여하여 다른 리소스 묶이지 않도록 할 수 있음
  * [링크](https://projectreactor.io/docs/core/release/reference/#faq.wrap-blocking)참고

### Schedulers.parallel()

* 병렬 작업에 맞게 고정된 워커풀을 생성
* CPU Core개수만큼 워커풀을 생성함

### 추가

* `Schedulers.fromExecutorService(ExecutorService)`를 사용하여 ExecutorService를 Scheduler로 사용할 수 있음
* `newXXX`를 사용하여 다양한 스케줄러 타입의 인스턴스를 생성할 수 있음
* boundedElastic()은 single과 parallel과 다르게 피할 수 없는 legacy blocking콜을 사용하는데
  * single과 perallel을 사용한다면 blocking api(block(), blockFirst(), blockLast())를 사용하면 IllegalStateException을 발생시킴
* 어떤 연산자는 기본으로 특정 Schduler를 사용함
  * ex) Flux.interval는 Schedulers.parallel()을 사용함
    * 변경가능함

## publishOn, subscribeOn

* Reactor에서는 Reactor chain 내에서 실행 컨택스트를 바꿀 수 있는 publishOn과 subscribeOn을 제공함

### publishOn

* 다른 오퍼레이터들과 같이 체인 내에서 적용될 수 있음
* 연관된 Scheduler의 worker에서 콜백이 실행되는 동안 업스트림에서 신호를 받아, 다운스트림으로 재생해줌
  * 따라서 후속 오퍼레이터가 실행되는 곳에 영향을 미침
* 스케줄러에서 선택된 하나의 쓰레드로 실행 컨텍스트를 변경함
* 시퀀스 내에서 onNext를 호출하면 선택된 쓰레드로 실행됨
* 이후 특정 스케줄러를 지정하지 않는 한, publishOn 이후의 연산자는 동일한 쓰레드에서 실행됨

```
Scheduler s = Schedulers.newParallel("parallel-scheduler", 4); 1번 쓰레드

final Flux<String> flux = Flux 
	.range(1, 2) // 2번 쓰레드로 실행
	.map(i -> 10 + i) // 2번 쓰레드로 실행 
	.publishOn(s) 
	.map(i -> "value " + i); // 1번 쓰레드로 실행
	
new Thread(() -> flux.subscribe(System.out::println)); - 2번 쓰레드
```

### subscribeOn

* backward chain이 구성될때, 구독처리과정에서 subscribeOn을 적용함
* 중간 연산자가 실행컨택스트에 영향을 줄 수 있으므로 데이터 소스 바로 뒤에 적용하는 것이 좋음
* publishOn동작에는 영향을 미치지 않음
* 구독하는 전체 체인의 Scheduler의 쓰레드를 변경함
* 스케줄러에서 하나의 쓰레드를 선택함

```
Scheduler s = Schedulers.newParallel("parallel-scheduler", 4); - 1번 쓰레드

final Flux<String> flux = Flux 
	.range(1, 2) 
	.map(i -> 10 + i) // 1번쓰레드로 실행
	.subscribeOn(s) // 1번쓰레드로 실행
	.map(i -> "value " + i); // 1번쓰레드로 실행

new Thread(() -> flux.subscribe(System.out::println)); - 2번쓰레드
```

* subscribeOn이 있다면 어디서 구독하든 subscribeOn의 Scheduler를 사용하여 시작함

* 스케줄러 공부자료 [링크](https://wiki.terzeron.com/Programming/Java/Reactor_Flux%EC%9D%98_publishOn_subscribeOn%EC%9D%84_%EC%9D%B4%EC%9A%A9%ED%95%9C_%EC%8A%A4%EC%BC%80%EC%A5%B4%EB%A7%81)참고

## Sink

* signal을 수동으로 트리거 할 수 있는 구조
* 여러 Subscriber를 처리할 수 있는 Publisher구조를 가짐
  * unicast()는 아님

## Hot vs Cold

### Cold

![center|400](/image/real-resource-image/Pasted%20image%2020231218210001.png)

* 구독이 생성될때마다 새로운 데이터를 생성
* 구독이 생성되지 않으면, 데이터를 생성하지 않음
* HTTP 요청에 비유하면, 구독이 생성될때마다 HTTP 요청을 보냄

### Hot

![center|400](/image/real-resource-image/Pasted%20image%2020231218210038.png)

* 구독자의 수에 영향을 받지 않음
* 즉시 데이터를 publishing 할 수 있음
* 구독하기 전에 실제로 어떠한 일이 발생할 수 있음
* 데이터 생성 중간에 새로운 구독자가 들어오면, 새로온 구독자는 그전의 데이터를 알지 못하고, 구독 이후에 데이터만 전달받음
* Hot publisher의 예시로 just가 있는데 어셈블리 시점에 데이터를 캡처하고, 나중에 오는 구독자에게 이를 생성함
* HTTP요청에 비유하면, 인스턴스화 할때 네트워크 요청이 한번 실행되고 구독자에게 이 결과를 전달함
  * 구독이 늘어난다고 해서 HTTP요청을 여러번 실행하지 않음

> just를 cold publisher로 변환하려면 defer를 사용하면 됨\
> share 또는 replay를 사용한다면 cold publisher를 hot publisher로 전환가능
>
> * [관련공부자료](https://binux.tistory.com/135)

## default scheduler 변경방법

* Schedulers.Factory를 사용하여 가능
* [링크](https://projectreactor.io/docs/core/release/reference/#scheduler-factory) 참고

## Context

* 명령형 프로그래밍에서 사용하는 Thread Local의 대안
* 세부사항
  * Map자료 구조와 유사함
  * key와 value가 Object, Object타입으로 다양한 값들을 넣을 수 있음
    * 다양한 라이브러리들과 호환 위해
  * 불변임, put 또는 putAll이 발생한다면 새로운 인스턴스가 생성됨
  * ContextView인 read only api는 write와 관련된 메서드를 제공하지 않음
* 실제로 체인에 있는 구독자에게 연결됨
* 구독 전파매커니즘을 통해 최종 구독자로부터 위로 올라가면서 각각의 operator가 Context를 사용하게 해줌
* *inner sequence에서 외부의 context를 읽을 수 있음*
* thread local 데이터를 자동으로 context에 넣어주는 Context-Propagation관련 기능도 있음 [링크](https://projectreactor.io/docs/core/release/reference/#context.propagation)참고
* context 공부 예제 [링크](https://devfunny.tistory.com/916) 참고

https://projectreactor.io/docs/core/release/reference/index.html\
https://brunch.co.kr/@springboot/153
