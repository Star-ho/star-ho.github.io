---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8600
tags:
  - Coroutine
  - hugo_blog
categories: Coroutine
title: 2-CoroutineScope
lastmod: 2024-04-21T09:36:36.362Z
---
* 새로운 coroutine에 대한 Scope를 정의함
* launch와 async와 같은 coroutine builder는 CoroutineScope의 확장함수임
* 팩토리 메서드인 CoroutineScope()와 MainScope()로 standalone CoroutineScope생성
  * 더 이상 필요가 없을때는 메모리누수 방지를 위해 cancel을 사용해야함
* 모든 Coroutine builder(async, launch 등)와 모든 scope function(coroutineScope, withContext 등)은 실행하는 코드 내부 블록에 자체 Job 인스턴스와 함께 자신들의 scope를 제공
  * 관습적으로 모든 coroutine builde와 scope function은 내부의 모든 coroutine이 완료될때까지 기다렸다가 완료하는 구조화된 동시성을 제공

https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/\
https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/\
https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope.html
