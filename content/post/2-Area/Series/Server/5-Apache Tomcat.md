---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8670
tags:
  - Server-History
  - hugo_blog
categories: Server-History
title: 5-Apache Tomcat
lastmod: 2024-04-21T09:36:36.374Z
---
* Jakarta Servlet, Jakarta Server Page, Jakarta Expression Language, Jakarta WebSocket, Jakarta Annotations, Jakarta Authentication 의 세부사항을 구현한 오픈소스 소프트웨어

## Architecture

![center](/image/real-resource-image/Pasted%20image%2020231125230312.png)

### Server

* Server는 웹 컨테이너 자체를 의미
* 사용자가 거의 customize하지 않는 설정들의 기본 구현들을 제공

### Service

* Server내에있는 중간 구성 요소(intermediate component)로 하나 이상의 커넥터를 정확히 하나의 엔진과 연결시킴
* 기본 구현들이 간단하고 충분하므로, Server와 마찬가지로 사용자들이 거의 customize하지 않음

### Engine

* 특정 서비스에 대한 요청 처리 파이프라인을 나타냄
* Service는 여러 connector를 가질 수 있고, Engine은 이러한 커넥터로부터 온 모든 요청을 수신하고 처리하여, 클라이언트에 전달한 적절한 커넥터로 응답을 다시 전달함
* Engine 인터페이스는 사용자에 의해 구현될 수 있지만, 자주 발생하는 일은 아님

### Host

* 호스트는 네트워크 이름(예: www.yourcompany.com)을 Tomcat 서버에 연결한 것
* 엔진에는 여러 개의 호스트가 포함될 수 있으며, 호스트 요소는 yourcompany.com 및 abc.yourcompany.com과 같은 네트워크 별칭도 지원
* 표준 호스트 구현은 상당한 추가 기능을 제공하기 때문에 사용자가 사용자 지정 호스트를 만드는 경우는 거의 없음

### Context

* 웹 어플리케이션을 나타냄
* 호스트에는 고유한 경로를 가진 여러개의 context를 가질 수 있음
* 사용자정의 컨텍스트를 생성하기 위해 인터페이스를 구현할 수 있지만, 표준 컨텍스트가 충분히 많은 기능을 제공하기에 이런 경우는 드뭄

### Connector

* 클라이언트와 통신을 처리
* HTTP 트래픽에 처리되는 HTTP 커넥터와 HTTPD와 같은 웹서버와 연결할때 필요한 ajp프로토콜을 구현한 ajp커넥터가 존재함

https://tomcat.apache.org/tomcat-9.0-doc/architecture/overview.html\
https://medium.com/chequer/tomcat-spring-bootstrapping-sequence-1%ED%8E%B8-tomcat-4402102c0585
