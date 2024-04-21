---
date: 2024-03-31T22:41:00
updatedAt: 2024-04-21 18:32:05+2150
tags:
  - Server-History
  - hugo_blog
  - virtual-thread
categories: Server-History
title: 13-Virtual Thread
lastmod: 2024-04-21T09:32:10.689Z
---
## Platform Thread

![Pasted image 20231223233300](/image/real-resource-image/Pasted%20image%2020231223233300.png)

* 기존 Java의 Thread 모델
* 하나의 java쓰레드에 OS Thread를 할당해서 사용
* 새로운 쓰레드가 필요하면 os에 요청해서 가져옴

## Virtual Thread

![Pasted image 20231223233244](/image/real-resource-image/Pasted%20image%2020231223233244.png)

* Virtual Thread의 쓰레드 모델
* Virtual Thread 생성시 OS에 요청해서 생성하지 않고. java library에서 생성
* 실제 OS쓰레드에는 Carrier Thread가 매핑이 되고, Carrier Thread를 가지고 있는  Virtual Thread가 현재 실행되고 있는 쓰레드임
  * Carrier Thread가 없는 Virtual Thread는 동작중이지 않음
* 쓰레드 관리를 JVM에서 함
  * 컨텍스트 스위칭 처리를 OS단이 아닌 JVM내에서 처리함

> 버추얼 쓰레드가 블럭킹 되면
>
> * 해당하는 코드 찾기\
>   버추얼 쓰레드 스케줄링이\
>   기존 쓰레드 언마운트하고\
>   다른 캐리어 쓰레드로 연결해줌

## 비교

![Pasted image 20231223233119](/image/real-resource-image/Pasted%20image%2020231223233119.png)

### 유의사항

* CPU bound한 상황에서는 Platform Thread가 더 나은 성능을 보여줌
* 정말 스트리밍 데이터를 사용한다면 reactor를 고려하자
* syncronized 또는 JNI call 시  carrier thread에 블로킹(pinning)이 발생
  * syncronized을 reenterantLock으로 변경
* thread local데이터를 Heap에 저장하므로 무분별하게 사용시 OOM발생 가능성이 있음

https://techblog.woowahan.com/15398/\
https://tech.kakao.com/2023/12/22/techmeet-virtualthread/\
https://medium.com/deno-the-complete-reference/springboot-virtual-threads-vs-webflux-performance-comparison-for-jwt-verify-and-mysql-query-ff94cf251c2c\
https://www.diva-portal.org/smash/get/diva2:1763111/FULLTEXT01.pdf\
https://docs.oracle.com/en/java/javase/20/core/virtual-threads.html#GUID-2BCFC2DD-7D84-4B0C-9222-97F9C7C6C521\
https://github.com/brettwooldridge/HikariCP/issues/2151\
https://perfectacle.github.io/2022/12/29/look-over-java-virtual-threads/

### deep-dive-link

https://blog.rockthejvm.com/ultimate-guide-to-java-virtual-threads/\
https://openjdk.org/jeps/425\
https://softwaremill.com/what-is-blocking-in-loom/\
https://wiki.openjdk.org/display/loom/Main\
https://stackoverflow.com/questions/74581601/how-does-a-java-virtual-thread-know-when-the-thread-is-waiting\
https://stackoverflow.com/questions/70174468/project-loom-what-happens-when-virtual-thread-makes-a-blocking-system-call\
\#need-to-deep-dive
