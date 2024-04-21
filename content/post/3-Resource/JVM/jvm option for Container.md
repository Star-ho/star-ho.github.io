---
date: 2023-11-14T11:26:23
updatedAt: 2024-04-21 18:34:36+3290
tags:
  - JVM
  - JVM-option
  - hugo_blog
categories:
  - JVM
title: jvm option for Container
lastmod: 2024-04-21T09:36:38.177Z
---
### 현재 옵션 확인

* java -XX:+PrintFlagsFinal -version 2>&1 | grep -i -E 'heapsize|metaspace|version'

### Memory

* 고정값으로 설정
  * -Xmx(ex -Xmx4g)
    * 최대값
  * -Xms(ex -Xms500m)
    * 최소값
* 비율로 설정
  * -XX:MaxRAMPercentage(-XX:MaxRAMPercentage=75),
    * 최대값
  * -XX:InitialRAMPercentage(-XX:InitialRAMPercentage=75)
    * 최소값
* 메모리 설정시 팁!
  * container메모리와 heap은 같은 사이즈로 가면 안됨
    * java에는 heap말고 non-heap도 있기때문
    * 힙 공간 외에도 Java 스레드, 가비지 컬렉션, 메타스페이스, 네이티브 메모리, 소켓 버퍼를 위한 공간이 필요하기 때문
    * 컨테이너 메모리와 같은 크기로 했을 때 컨테이너도 crash 발생하기 때문에 oom 후처리가 안됨
  * percentage로 75%정도 권장
    * [ms홈페이지 참고](https://learn.microsoft.com/en-us/azure/developer/java/containers/overview)
    * 물론 절대적인건 아님
  * 하나의 컨테이너에 하나의 어플리케이션만 돌아갈떄 min size와 max size를 같게 설정
    * 메모리가 부족이 발생하면 os에 메모리를 더 달라고 요청하는데, 어차피 하나의 컨테이너에 하나의 프로세스만 돌아가는데 굳이 필요없는 요청을 늘릴 필요가없음
    * gc가 더 자주 실행됨
    * 하나의 컨테이너에서 하나의 프로세스만 돌기 때문에 경쟁이 필요가 없음

### OOM 처리

* -XX:+HeapDumpOnOutOfMemoryError
  * OOM이 발생하면 heap dump를 생성
* -XX:HeapDumpPath=/var/log
  * 힙 덤프 생성 위치 설정
  * 위의 아래에 .hprof파일 생성됨

> MaxRAMFraction
>
> * deprecated된 옵션
> * max heap memory를 설정하는 다른 방법

> InitialRAMPercentage, MinRAMPercentage관련한 글\
> https://blog.gceasy.io/2020/11/05/difference-between-initialrampercentage-minrampercentage-maxrampercentage/\
> https://blog.ycrash.io/2020/11/23/best-practices-java-memory-arguments-for-containers/

\#JVM-option

https://docs.oracle.com/en/java/javase/17/docs/specs/man/java.html\
https://www.merikan.com/2019/04/jvm-in-a-container/\
https://learn.microsoft.com/en-us/azure/developer/java/containers/overview\
https://stackoverflow.com/questions/43651167/is-there-any-advantage-in-setting-xms-and-xmx-to-the-same-value\
https://developer.jboss.org/thread/149559\
https://www.codementor.io/@suryab/outofmemoryerror-related-jvm-arguments-w6e4vgipt\
https://medium.com/nordnet-tech/setting-java-heap-size-inside-a-docker-container-b5a4d06d2f46\
https://dzone.com/articles/best-practices-java-memory-arguments-for-container\
https://docs.oracle.com/en/java/javase/17/docs/specs/man/java.html#overview-of-java-options\
https://d2.naver.com/helloworld/37111
