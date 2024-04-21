---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8690
tags:
  - Server-History
  - hugo_blog
  - Reactive
categories: Server-History
title: 10-Reactor Java(1)
lastmod: 2024-04-21T09:36:36.380Z
---
* callback보다 나은점
  * callback hell이 발생하지 않음
* Future보다 나은점
  * 더 많은 연산자를 지원함

## Flux

![Pasted image 20231208223306](/image/real-resource-image/Pasted%20image%2020231208223306.png)

* Flux\<T>는 0개에서 N개의 비동기 시퀀스 항목을 방출하는 Publisher\<T>임
* onComplete 혹은 onError로 종료됨
* `onNext`, `onComplete`, `onError` 로 downstream을 호출할 수 있음
* 종료를 포함한 모든 이벤트는 선택사항힘
  * onNext가 없고 onComplete만 있다면 빈 유한 시퀀스임
  * onNext 있고 onComplete가 없다면 무한 시퀀스임

## Mono

![Pasted image 20231208224210](/image/real-resource-image/Pasted%20image%2020231208224210.png)

* 최대 하나의 항목만을 emit하는 Publisher
* onNext이후 onComplete가 호출되거나 하나의 onError가 호출됨
* Mono는 Flux에 비해 제한된 연산자를 제공함
  * 하나의 항목만 방출하니까
* Mono#concatWith(Publisher) 등 여러 메스드로 Mono를 Flux로 변경가능함
* Mono\<Void>로 완료개념만 있는 비동기 시퀀스를 생성가능

## 그외 개념

### Hot Sequence vs Cold Sequence

* Hot Sequence
  * 한번 구독하면 생성된 시퀀스를 재사용함
  * 나중에 구독한 구독자는 이전꺼 시퀀스를 받지 못하고 구독이후의 시퀀스를 받을 수 있음
* Cold Sequence
  * 구독할때마다 시퀀스가 재 생성됨

### Disposable

* subscribe()이 리턴하는 값
* Disposable을 사용해서 cancel가능
* Disposables.composite()를 사용해서 Disposable을 묶을 수 있으며 한번에 취소가능

### BaseSubscriber

* Reactor에서 사용자 정의 Subscriber를 구현할 수 있는 추상클래스
* BaseSubscriber를 사용하여 Subscriber를 lambda가 아닌 클래스로 구성가능
* requestUnbounded()를 통해 unbounded한 reqeust를 구현가능
  * request(Long.MAX\_VALUE)과 동일

### Backpressure

* reactor에서 Backpressure를 구현하는 방법은 request를 사용하는것
* 최대 Long.MAX\_VALUE까지 가능함

### DownStream에서 Demand가 바뀔 수 있음

* request로 몇개의 item을 받을 것인지 결정됨
* 하지만 중간에 buffer와 같은 연산자로 생산되는 item이 바뀔 수 있음
  * request가 2고 중간에 버퍼가 2라면 2\*2인 4개의 item이 생산됨
* limitRate, prefetch라는 개념도 있음
  * [링크](https://projectreactor.io/docs/core/release/reference/#_operators_that_change_the_demand_from_downstream)참고

### Sequence를 생성하는법

* generate
  * 동기적으로 하나하나씩 생성
* create
  * 비동기, 멀티쓰레드 방식으로 생성
  * Flux sink를 사용
* push
  * 비동기, 싱글쓰레드 방식으로 생성

> create와 push를 사용한 후에는 onCancel과 onDispose로 끝내야함

* handle
  * 인스턴스 메소드로 동기로 하나씩 생성된 것들에 대해 값을 변형하거나, 필터링할 수 있음
