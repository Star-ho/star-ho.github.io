---
date: 2023-10-03T22:58:54
updatedAt: 2024-04-21 18:34:36+2730
tags:
  - Concept
  - hugo_blog
  - Network
title: Forward Proxy, Reverse Proxy
lastmod: 2024-04-21T09:36:38.056Z
---
## Forward Proxy

![Pasted image 20231003225905](/image/real-resource-image/Pasted%20image%2020231003225905.png)

> A: 사용자의 가정용 컴퓨터\
> B: 정방향 프록시 서버\
> C: 웹 사이트의 원본 서버(웹 사이트 데이터가 저장되는 곳)

* 일반적으로 사용되는 프록시 서버를 의미
* 프록시가 없다면 A가 C로 직접 요청
* Forward Proxy가 있다면 A가 B로 요청하고 B가 다시 C로 호출함,
  * 응답은 C->B->A순
* 사용이유
  * 주 당국 또는 기관의 검색 제한을 피하기 위해
  * 온라인에서 자신의 신원을 보호하기 위해

## Reverse Proxy

![Pasted image 20231003225917](/image/real-resource-image/Pasted%20image%2020231003225917.png)

> D: 사용자의 가정용 컴퓨터\
> E: 역방향 프록시 서버\
> F: 웹 사이트의 원본 서버(웹 사이트 데이터가 저장되는 곳)

* 인터넷과 서버를 연결할 때 대신 요청을 받아 처리
* Forward Proxy는 클라이언트 보호가 목적이었다면, Reverse Proxy는 서버 보호가 목적
* 사용이유
  * 부하분산
  * 캐싱
  * SSL 암호화

<https://www.cloudflare.com/ko-kr/learning/cdn/glossary/reverse-proxy/>

\#Concept
