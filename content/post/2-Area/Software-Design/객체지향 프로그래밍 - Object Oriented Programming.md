---
date: 2024-06-30 23:17:10
updatedAt: 2024-09-18 23:40:50
tags:
  - OOP
  - hugo_blog
categories:
  - Concept
title: 객체지향 프로그래밍 - Object Oriented Programming
lastmod: 2024-09-18T14:41:02.500Z
---
## 개념

* 프로그래밍 패러다임의 한 종류로, 객체간의 상호작용을 통해 로직을 구성하는 방법
* 객체간의 상호작용은 인간 세계의 상호작용을 바탕으로 하기에 이해하기 쉬움

## 특징

### 캡슐화

* 서로 연관되어있는 속성과 기능들을 하나의 캡슐로 만들어 외부로 부터 보호하는것
  * 데이터 보호 - 외부로부터 클래시에 정의된 속성과 기능들을 보호
  * 데이터 은닉 - 내부의 동작을 감추고 외부에는 필요한 부분만 노출

### 추상화

* 중요한 부분을 강조하기 위해 불필요한 세부사항을 제거하고, 본질적이고 공통적인 부분만 추출하여 표현하는 것

### 상속

* 상위 클래스로부터 확장된 여러 개의 하위 클래스들이 모두 상위 클래스의 속성과 기능들을 간편하게 사용할 수 있도록 하는것
  * 속성보다는 하는 행위가 같아야지 덜 복잡함

### 다형성

* 어떤 객체의 속성이나 기능이 상황에 따라 여러 가지 형태를 가질 수 있는 성질
  * 메서드 오버라이딩, 메서드 오버로딩

## 원칙

### 단일 책임 원칙 (Single Responsiblity Principle)

* 하나의 객체는 하나의 책임만 가져야함
* 변경의 이유도 하나여야함

### 개방-폐쇄 원칙 (Open Closed Principle)

* 변경에는 닫혀있고, 확장에는 열려있어야함

### 리스코프 치환 원칙 (Liskov Substitution Principle)

* 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 함

### 인터페이스 분리 원칙 (Interface Segregation Principle)

* 필요한 인터페이스만 상속받아야함
  * 특정 클라이언트를 위한 인터페이스 여러개가 범용 인터페이스 하나보다 낫다

### 의존 역전 원칙 (Dependency Inversion Principle)

* 구체화된것 보다는 추상적인것을 의존해야함

어떤 코드가 변경되었을때 변경되는 정도\
결합도\
응집도

관심사의 분리\
https://www.cs.utexas.edu/users/EWD/transcriptions/EWD04xx/EWD447.html\
It is what I sometimes have called "the separation of concerns", which, even if not perfectly possible, is yet the only available technique for effective ordering of one's thoughts, that I know of. This is what I mean by "focussing one's attention upon some aspect": it does not mean ignoring the other aspects, it is just doing justice to the fact that from this aspect's point of view, the other is irrelevant. It is being one- and multiple-track minded simultaneously.

https://www.codestates.com/blog/content/%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8A%B9%EC%A7%95


