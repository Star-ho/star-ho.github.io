---
date: 2024-03-31T22:41:00
updatedAt: 2024-04-21 18:32:05+2430
tags:
  - Testing
  - hugo_blog
categories:
  - Test
title: Test Double
lastmod: 2024-04-21T09:32:12.943Z
---
## Fixture

* 소프트웨어를 일관되게 테스트 할 수 있게 해주는 장치
* 테스트를 실행하고, 특정결과를 예상하기 위해 필요한 모든것 [링크](http://xunitpatterns.com/test%20fixture%20-%20xUnit.html)
* 테스트 더블과는 다른 용어임

> SUT(System Under Test)\
> 우리가 테스트하고 싶어하는 모든것, 테스트 관점에서 정의됨\
> 단위테스트를 작성할 때는 테스트를 하는 클래스(CUT), 객체(OUT), 메서드(OUT),\
> 통합 테스트를 작성할때는 어플리케이션(AUT)

## Test Double

* 테스트 목적으로 실제 객체 대신 올 수 있는 모든 종류의 객체를 의미함
* 테스트를 진행하기 어려운 경우, 이를 대신해 테스트를 진행할 수 있도록 만들어 주는 객체

> 영화 촬영시 위험한 역할을 대신한느 스턴트 더블에서 비롯됨

* Dummy Object, Test Stub, Test Spy, Mock Object, Fake Object 과 같은 구현이 존재함

![center](/image/real-resource-image/Pasted%20image%2020240409231740.png)

## Dummy

* SUT의 메소드 시그니처에 필요한 객체들
* 테스트에 영향은 가지 않지만, SUT 메소드 시그니처에 필요한 객체
* null 오브젝트, 하드코딩된 값들

> 실제로 테스트에 영향이 가지않아, Test Double로 보지않는 의견도 있음

## Test Stub

* SUT가 의존하는 실제 컴포넌트를 대체하기 위해 사용
* 실제 컴포넌트를 대체하여 간접적인 입력에 대해 제어할 수 있음

## Test Spy

* Test Stub보다는 더 강력한 버전의 Test Double로써, SUT 간접적인 입력의 관찰지점(observation point)임
* 간접적인 입력에 대해 관찰이 가능하며, SUT에 대한 간접적인 출력을 capture함
  * capture한 간접적인 출력에 대해 검증 가능
* Mock Object와 동일한 목적으로 사용하지만, Test Spy를 사용한다면, 테스트 스타일은 Test Stub과 더 유사함

## Mock Object

* SUT의 간접적인 출력을 검증할 수 있는 관찰지점
* 테스트에 실패하지 않은 경우, SUT에 반드시 값을 리턴하는, Test Stub의 기능을 포함함
* 간접 출력의 검증에 더 치우쳐져 있음
* 단순히 Test Stub에 assertion이 추가된것이 아닌, 근본적인 차이가 있음

## Fake Object

* SUT의 간접 입력 및 출력 확인하는 것 이외의 이유로 실제 의존하는 객체의 기능을 대체하기 위해 사용
* 실제 객체와 동일한 기능을 하지만, 간단하게 구현됨
* 테스트를 위해 특별히 구축 되지만, 제어지점이나 관찰지점으로 사용되지 않음
* 실제 의존하는 객체가 아직 사용가능하지 않거나, 테스트에 사용할 수 없을때 Fake Object를 사용함
* 데이터베이스를 인메모리 해시 테이블로 대체하여 테스트를 50배 빠르게 실행 가능하도록 할 수 있음

https://martinfowler.com/articles/mocksArentStubs.html\
http://xunitpatterns.com/Test%20Double.html
