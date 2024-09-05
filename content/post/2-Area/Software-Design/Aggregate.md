---
date: 2024-03-02T22:41:32
updatedAt: 2024-07-11 12:01:28
tags:
  - Software-design
  - hugo_blog
  - DDD
categories: Software-Design
title: Aggregate
lastmod: 2024-07-11T03:05:37.484Z
---
* 데이터의 변경단위로 다루는 연관 객체의 묶음
* Root Entity와 Boundary Entity가 존재
  * Root Entity
    * 전역 식별성을 가짐
  * Boundary Entity
    * 지역 식별성을 가짐
    > 매우 상대적인 개념\
    > 자동차와 바퀴의 예시\
    > 자동차에만 관심만 있고 바퀴에 대한 관심사가 따로 없을 경우, 루트엔티티는 자동차, 경계 엔티티는 바퀴가됨\
    > 하지만 바퀴의 수명주기, 일련번호 어떤 타이어를 찾아 어느 자동차에 있는지 알아야하는 비즈니스가 생긴다면 바퀴또한 루트 엔티티가 될 수 있음

### Aggregate에 적용되어야 하는 규칙

1. 루트 Aggregate는 전역 식별성을 가지며 궁극적으로 불변식을 검사할 책임이 있다.
2. 경계안의 Entity는 지역식별성을 지니며, 이러한 지역식별성은 해당 Aggregate안에서만 유일하다.
3. Aggregate 경계 밖에서는 루트 Entity를 제외한 Aggregate내부의 구성요소를 참조할 수 없다.
4. Aggregate안의 객체는 다른 Aggregate의 루트만 참조할 수 있다.
5. 삭제연산은 Aggregate안의 모든 요소를 제거해야 한다.
6. Aggregate경계 안의 어떤 객체를 변경하더라도 전체 Aggregate의 불변식은 지켜져야 한다.
