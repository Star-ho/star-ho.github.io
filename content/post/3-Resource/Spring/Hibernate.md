---
date: 2024-01-19T22:29:15
updatedAt: 2024-04-21 18:34:36+3320
tags:
  - Spring
  - ORM
  - hugo_blog
categories:
  - Spring
title: Hibernate
lastmod: 2024-04-21T09:36:38.180Z
---
### Hibernate

* Java로 작성된 프로그램에서 관계형 데이터를 자연스럽고, typesafe한 형태로 사용할 수 있게함
* 복잡한 쿼리를 작성하고, 그 결과를 쉽게 사용할 수 있도록함
* 프로그램이 메모리에 변경된 내용을 쉽게 데이터베이스와 쉽게 동기화 하고 ACID속성을 준수함
* 기본 영속성 로직이 작성된 후에 성능을 최적화 할 수 있음
* JPA에서 영감을 얻었으며, JPA의 최신 버전 specification을 구현

## Persistence Context

* first-level cache라 불리는 일종의 캐시
* 영속 컨텍스트 내에서 데이터베이스에서 읽은 모든 엔티티 인스턴스와 새로 생성된 엔티티들에 대해, 영속성 컨택스트는 엔티티 인스턴스 식별자와 인스턴스 자체에 대한 고유한 매핑을 보유
* persistence Context와 연관된 entity는 아래 4가지중 하나의 상태를 가짐 상태
  * transient
    * 초기화 되었지만 영속성컨텍스트와 연관되지 않은 상태
    * 데이터베이스에 나타나지 않았고, 식별자가 할당되지 않은 상태
  * managed or persistent
    * 연관된 식별자가 있으면서, 영속성 컨텍스트에 연관되어 있음
    * 물리적으로 데이터에 존재할수도, 안할 수 도 있음
  * detached
    * 연관된 식별자가 있으면서, 영속성 컨텍스트와 연관되어있지 않은 상태
    * 일반적으로, 영속성 컨택스트가 닫혔거나, 인스턴스가 컨텍스트에서 제외된 경우
  * removed
    * 연관된 식별자를 가지며, 영속성 컨텍스트에 연관되어 있지만, 데이터베이스에서 삭제가 예약되어 있는 상태

## Persistence context의 operation

* persist(Object)
  * transient 객체를 영속화 하고, SQL insert문을 예약함
* remove(Object)
  * 영속 객체를 transient하게 만들고, SQL delete 문을 예약함
* merge(Object)
  * 주어진 detached된 객체를 영속 객체로 복사하고, 영속 객체를 반환
* detach(Object)
  * 데이터베이스에 영향을 주지 않고 세션과 영속객체의 연결을 분리함
* clear()
  * 영속성 컨텍스트를 비우고, 모든 엔티티들을 detache함
* flush
  * 세션과 연결된 영속객체의 변경을 확인하고, insert, update문을 통해 데이터베이스와 세션의 상태를 동기화함

https://docs.jboss.org/hibernate/orm/6.4/introduction/html\_single/Hibernate\_Introduction.html\
https://docs.jboss.org/hibernate/orm/6.4/userguide/html\_single/Hibernate\_User\_Guide.html
