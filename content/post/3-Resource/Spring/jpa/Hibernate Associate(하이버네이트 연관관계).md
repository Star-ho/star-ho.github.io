---
date: 2024-01-23T22:39:11
updatedAt: 2024-04-21 18:34:36+3320
tags:
  - Spring
  - ORM
  - hugo_blog
categories:
  - Spring
title: Hibernate Associate(하이버네이트 연관관계)
lastmod: 2024-04-21T09:36:38.180Z
---
## 연관관계의 소유

* 소유라는 개념은, 외래키를 관리하는 주체를 의미함
* 단방향에서는 소유하는 쪽만 존재
* 양방향에서는 소유하는쪽과 그 반대쪽 둘다 존재
  * manyToOne에서는 many쪽이 소유측을 가지고 있음
  * oneToOne에서는 외래키가 있는쪽이 소유측임
  * ManyToMnay에서는 둘 다 소유측이 될 수 있음
* mappedBy로 관계의 소유자인 엔티티의 속성 또는 필드를 지정함

## Cascade

* ALL, DETACH, MERGE, PERSIST, REFRESH, REMOVE 가 있으며 ALL은 다른 모든 속성을 포괄하는 개념
* 부모엔티티의 변경이 발생할때 자식까지 전파할건지에 대한 여부

> ex) CascadeType.Merge가 지정된경우 부모가 merge되면 자식도 merge됨

## FetchType

* 해당 필드를 가져올 시기를 결정
* Lazy
  * oneToMany의 default FetchType
  * 해당 필드를 엑세스할때 가져옴
* Eager
  * ManyToOne의 default FetchType
  * 소유자의 로딩의 일부로 해당 필드를 가져옴
    * 소유자 로딩시 바로 가져옴\
      \*\*fetch=EAGER가 의미가 있는 유일한 시나리오는 연관된 객체가 두 번째 수준 캐시에서 발견될 확률이 항상 매우 높다고 생각하는 경우. \*\*

> 꺠알팁\
> oneToMany, manyToOne설정된 필드에는 Column어노테이션 불가, joinColumn사용해야함

https://docs.jboss.org/hibernate/orm/6.4/introduction/html\_single/Hibernate\_Introduction.html#associations\
https://docs.jboss.org/hibernate/orm/6.4/userguide/html\_single/Hibernate\_User\_Guide.html#associations-many-to-one\
https://docs.oracle.com/javaee/7/tutorial/persistence-intro001.htm#JEETT01154
