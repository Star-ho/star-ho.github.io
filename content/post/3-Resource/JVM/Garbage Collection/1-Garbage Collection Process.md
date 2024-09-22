---
date: 2024-02-22T23:15:48
updatedAt: 2024-09-22 15:57:23
tags:
  - JVM
  - Garbage-Collection
  - hugo_blog
categories:
  - JVM
  - JVM/GC
title: 1-Garbage Collection Process
lastmod: 2024-09-22T06:57:23.034Z
---
* 앞의 Garbage Collection Concept에서는 heap이 나누어져 저장되는 것을 이해함
* 여기서는 나누어져 저장되는 것들의 상호작용에 대해 알아봄

### 1. Object Allocation

![center|600](/image/real-resource-image/Pasted%20image%2020240222231607.png)

* 모든 새 객체들은 Eden 영역에 할당됨
* 애플리케이션을 처음 시작한다면 두 survivor영역은 비어있음

### 2. Filling the Eden Space

![center|600](/image/real-resource-image/Pasted%20image%2020240222232133.png)

* Eden 영역이 꽉 찬다면, minor GC가 실행됨

### 3. Copying Referenced Objects

![center|600](/image/real-resource-image/Pasted%20image%2020240222232238.png)

* Referenced 객체는 S0 servivor 영역으로 이동됨
* Unreferenced 객체는 삭제됨

### 4. Object Aging

![center|600](/image/real-resource-image/Pasted%20image%2020240222232408.png)

* 다음 miner GC때 3의 동작이 한번 더 발생됨
* Referenced 객체는 suvivor 영역으로 이동하고, Unreferenced 객체는 삭제됨
* 3과 다른점은 S0에 존재했던 객체들이 S1영역 으로 간다는 것
* S0에서 S1으로 이동한 객체는 이동하면서 1살을 더 먹음
* miner GC가 발생하므로서 Eden과 S0은 비워지고 S1에만 객체가 존재함

### 5. Additional Aging

![center|600](/image/real-resource-image/Pasted%20image%2020240222233018.png)

* 다음 minor GC때 4의 과정이 반복되며 나이를 먹음
* S1에 있던 Referenced 객체들이 S0으로 이동하며 나이를 먹고, Eden과 S2 영역은 비워짐

### 6. Promotion - 1

![center|600](/image/real-resource-image/Pasted%20image%2020240222233216.png)

* 계속 minor gc가 발생하고, 특정 임계값(예제에서는 8)을 넘은 객체들은 Old Generation(Tenured)영역으로 이동함

### Promotion -2

![center|400](/image/real-resource-image/Pasted%20image%2020240222233418.png)

* minor GC가 계속 발생하면서 Old Generation으로 객체가 계속 승격됨

### GC Process Summary

![center|600](/image/real-resource-image/Pasted%20image%2020240222233605.png)

* 결국에는 Old Generation에도 객체가 꽉차고 major GC가 발생함

https://d2.naver.com/helloworld/1329\
https://d2.naver.com/helloworld/0128759\
https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html\
https://developers.redhat.com/articles/2021/08/20/stages-and-levels-java-garbage-collection#generational\_garbage\_collection\
https://developers.redhat.com/articles/2021/09/09/how-jvm-uses-and-allocates-memory#how\_to\_check\_the\_thread\_stack\_size
