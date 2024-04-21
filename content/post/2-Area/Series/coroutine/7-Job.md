---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8540
tags:
  - Coroutine
  - hugo_blog
categories: Coroutine
title: 7-Job
lastmod: 2024-04-21T09:36:36.350Z
---
### JOB은 코루틴이 상속하지 않는 유일한 코루틴 컨텍스트임

## 생명주기

![center|600](/image/real-resource-image/Pasted%20image%2020240207224956.png)

* 위 그림과 같이 Job은 New, ACTIVE, COMPLETEING, COMPLETED, CANCELLING, CANCELLED 6가지 상태를 가진다

### ACTIVE

* 코루틴이 Job을 실행하고 있는 상태
* 대부분의 Coroutine은 Active상태로 시작

### NEW

* Job생성시 CoroutineStart.Lazy옵션을 주면 NEW상태로 Job이 생성됨
* ACTIVE상태가 되기위해서는 Job을 실행해야함

### COMPLETING

* 코루틴의 Job은 끝났고, 자식들의 종료를 기다리는 상태
* 자식들도 종료가 된다면 COMPLETED상태로 변경됨

### COMPLETED

* 코루틴의 JOB과 자식들이 정상적으로 종료된 상태
* final상태이므로 상태가 변경되지 않음

### CANCELLING

* Job이 실행도중 취소되거나 실패했을때 변경되는 상태
* 여기서 연결을 끊거나 자원을 바납하는 후처리 작업 가능
* 후처리 작업이 완료되면 CANCELLED상태로 변경됨

### CANCELLED

* CANCELLING에서 후처리 작업이 완료된 상태
* final상태이므로 상태가 변경되지 않음

https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/\
https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job.html\
https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/job.html
