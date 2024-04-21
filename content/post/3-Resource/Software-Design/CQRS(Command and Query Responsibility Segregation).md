---
date: 2023-09-24T23:14:02
updatedAt: 2024-04-21 18:34:36+3260
tags:
  - Software-design
  - hugo_blog
categories:
  - Software-Design
title: CQRS(Command and Query Responsibility Segregation)
lastmod: 2024-04-21T09:36:38.162Z
---
* 소프트웨어의 복잡성을 해결하는 방법중 하나
* 객체의 메서드를 Query와 Command로 분리하는것
* CQRS는 side effect 어떻게 다룰까에 관한 문제임

### Query

* 객체의 상태를 변경시키지 않고 특정한 값을 리턴하는 메서드
* 상태를 변경시키지 않기때문에 side effect가 발생하지 않음
* command에서 사용할 수 있음

### Command

* 객체의 상태를 변화시키는 메서드
* 리턴값이 존재하지 않음
* commnd는 객체의 상태를 변경하는 메서드이므로 side effect가 존재
* query에서 호출할 수 없음

### 장점

* 디버깅시 조회에 관한 부분은 Query에서, side effect에 관한 부분은 command만 검사하면 됨
* 복잡성이 줄어든다

### 단점

* 하지만 command임에도 리턴값이 필요한 상황이 존재한다
* ex) 최종 변경사항을 리턴해줘야하는 경우, Stack의 pop메서드
* command라는 네이밍을 가지고 메서드명도 충분히 command라는걸 표현함에도 이걸 쿼리처럼 사용하는 사람이 문제라는 결론

> Query
>
> * 하나의 값을 받아서 어떠한 처리를 하는 함수
> * side effect가 존재해서는 안됨
> * Procedure에서 호출될 수 있음

> Procedure
>
> * 하나의 값을 받아서 결과를 리턴해주는 함수
> * side effect가 존재함
> * Query에서 호출할 수 없음
>   * query에서는 side effect가 생기면 안되기 때문

https://martinfowler.com/bliki/CommandQuerySeparation.html\
https://blog.ploeh.dk/2014/08/11/cqs-versus-server-generated-ids/

\#Software-design
