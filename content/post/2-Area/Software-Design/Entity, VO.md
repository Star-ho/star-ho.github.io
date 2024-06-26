---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:36:36+4030
tags:
  - Software-design
  - hugo_blog
  - DDD
categories: Software-Design
title: Entity, VO
lastmod: 2024-04-21T09:36:36.403Z
---
## domain model

현실세계의 해결하고싶은 문제를 프로그래밍 언어로 특정 측면을 구현한것\
특정측면인 이유는 비즈니스에 중요한것을 더 부각하고, 중요하지않은것을 적게 표현하거나 생략하기 때문

## Entity

해당 객체의 식별성을 가질경우 그 객체를 Entity라 한다

### Entity의 상대성

Entity는 매우 상대적이다.\
경기장 좌석이 지정석인 경우, 각 좌석들은 구분해야할 필요가 있기 때문에 식별성을 가진 Entity이다.\
경기장 좌석이 자유석일경우, 각 좌석들의 구분하지않는다. 이는 Entity가 아니다.

## VO

모델에 포함된 어떤 요소의 식별성이 아닌, 속성에만 관심이 있다면 vo라고 한다

#### 불변의 VO

**vo는 불변이다**\
vo가 바뀔때 내부 속성이 바뀌는 것이 아닌, vo전체가 수정되어야 한다.\
but 변경가능성을 허용하는 케이스가 존재한다.\
ex) 자주 변경되는경우, 객체 생성이나 삭제에 비용이 많이드는 경우 등..

### 도메인 엔티티란?

* 현실세게의 해결하고 싶은 문제를, 프로그래밍 언어적으로 구현한 모델인데, 식별성을 가지는것
* 식별성을 가지지 않는다면, VO라고 부름

> 성능 최적화를 위해 Fly Weight를 사용할 수도 있다

> dto는?\
> data transform object로 계층간, 혹은 메서드간 데이터 이동시, 변수가 너무 많아지는 것 보다는 묶어서 변수들을 조금 더 잘 표현하는 역할
