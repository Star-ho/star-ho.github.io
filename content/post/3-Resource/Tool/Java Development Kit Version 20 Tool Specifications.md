---
date: 2023-10-04T23:04:11
updatedAt: 2024-04-21 18:34:36+2560
tags:
  - Tool
  - hugo_blog
  - JVM
title: Java Development Kit Version 20 Tool Specifications
lastmod: 2024-04-21T09:36:37.980Z
---
## 전체 tool 정보

* <https://docs.oracle.com/en/java/javase/20/docs/specs/man/index.html>

## 트러블 슈팅(troubleshooting) 시 유용할만한 툴

[jconsole](https://docs.oracle.com/en/java/javase/20/docs/specs/man/jconsole.html)

* start a graphical console to monitor and manage Java applications
* 자바 어플리케이션의 모니터링 및 관리할수 있게 도와주는 graphical 콘솔

[jmap](https://docs.oracle.com/en/java/javase/20/docs/specs/man/jmap.html)

* 프로세스 정보 제공
* heap dump뜰수 있음
* heap dump뜬거는 Eclips memory Analyzer로 분석
* jmap -dump:format=b,file=heapdump.hprof

[jps](https://docs.oracle.com/en/java/javase/20/docs/specs/man/jps.html)

* jvm에서 돌아가는 프로세스 조회

[jstack](https://docs.oracle.com/en/java/javase/20/docs/specs/man/jstack.html)

* thread dump뜰때 사용
* jstack

[jstat](https://docs.oracle.com/en/java/javase/20/docs/specs/man/jstat.html)

* jvm상태 모니터링

<https://tangoblog.tistory.com/16>

\#Java\
\#Jps
