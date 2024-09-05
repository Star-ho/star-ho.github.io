---
date: 2024-07-14 23:01:00
updatedAt: 2024-07-21 23:36:54
tags:
  - Network
  - Concept
  - hugo_blog
categories:
  - Network
title: HTTP Client Timeout
lastmod: 2024-07-21T14:36:54.734Z
---
![](/image/real-resource-image/Pasted%20image%2020240715122255.png)

## Connection Timeout

* client가 server에 커넥션을 연결을 시도하는 최대 시간 지정
  * the time to establish the connection with the remote host

## Socket Timeout

* server와의 연결이 완료된 시점에서, 데이터의 송수신을 할 최대 시간 지정
  * 두 데이터패킷 사이 비활성화된 최대 시간
  * maximum time of inactivity between two data packets

## HTTP Manager Timeout

* HTTP Client 라이브러리에서는 내부적으로 HTTP 커넥션 풀을 관리함
* HTTP 커넥션 풀에서 커넥션을 가져오는데 걸리는 최대 시간 제어

https://www.baeldung.com/httpclient-timeout\
https://stackoverflow.com/questions/18184899/what-is-the-difference-between-the-setconnectiontimeout-setsotimeout-and-http
