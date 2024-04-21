---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8740
tags:
  - Concurrency
  - hugo_blog
  - lock
  - Concept
categories: Concurrency
title: 1-Mutex(Lock)
lastmod: 2024-04-21T09:36:36.386Z
---
* **Mut**ual **ex**clusion의 약자
* 프로세스 간 동기화에 사용할 수도 있는 동기화 기본 형식입니다.
* 둘 이상의 스레드가 동시에 공유 리소스에 액세스해야 하는 경우 시스템은 한 번에 하나의 스레드만 리소스를 사용하도록 하기 위한 동기화 메커니즘
* 공유 리소스에 대한 단독 액세스 권한을 하나의 스레드에만 부여하는 동기화 기본 형식입니다.
* 스레드가 뮤텍스를 획득하면 첫 번째 스레드가 뮤텍스를 해제할 때까지 해당 뮤텍스를 획득하려는 두 번째 스레드가 일시 중단됩니다.

### 화장실 비유

* 화장실에 가고싶은데 키가 하나밖에 없는경우
* 키가 있어야만 화장실에 갈 수 있음
* 화장실에 아무도 없다면 키를 가져갈 수 있음
* 화장실에 사람이 있다면, 사람이 나와 키를 줄때까지 기다려야함

> Mutex와 Lock은 동일한 개념임\
> Mutex는 Lock보다는 시스템 전체에 적용될 수 있음

### 참고자료

https://learn.microsoft.com/ko-kr/dotnet/api/system.threading.mutex?view=net-7.0#remarks\
https://en.wikipedia.org/wiki/Lock\_(computer\_science)\
https://stackoverflow.com/questions/2332765/what-is-the-difference-between-lock-mutex-and-semaphore
