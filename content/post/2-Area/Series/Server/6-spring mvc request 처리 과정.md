---
date: 2024-03-04T22:41:06
updatedAt: 2024-09-21 23:54:19
tags:
  - Server-History
  - hugo_blog
  - Server
categories:
  - Server-History
title: 6-spring mvc request 처리 과정
lastmod: 2024-09-21T14:54:19.490Z
---
![center|600](/image/real-resource-image/Pasted%20image%2020231125233607.png)

### Filter

* Dispatcher Servlet으로 가기전 적용되는 필터
* 모든 request에 적용되는 필터
* 설정을 통해 특정 path로 갈때, 우선순위 등 여러 설정 가능

### DispatcherServlet

* request를 분석하여 처리를 위해 적절한 컨트롤러로 보냄
* 스프링은 FrontController패턴을 사용하여 하나의 Servlet(DispatcherServlet)만 사용
  * 여러 Servlet을 사용할경우 중복코드가 생성되기에 하나의 Servlet에서 요청을 처리함
  * 스프링은 하나의 Dispatcher Servle만 생성 [참고링크](https://stackoverflow.com/questions/23049736/working-with-multiple-dispatcher-servlets-in-a-spring-application)

### Hander Mapping

* request가 등록된 controller 중 어떤 controller와 매핑되는지 찾음

### Hander Adapter

* Hander Mapping으로 찾은 controller 호출

### View Resolver

* 전달받은 view name가지고 view를 찾음

https://terasolunaorg.github.io/guideline/1.0.1.RELEASE/en/Overview/SpringMVCOverview.html\
https://justforchangesake.wordpress.com/2014/05/07/spring-mvc-request-life-cycle/\
https://velog.io/@hsw0194/Spring-MVC-HandlerMapping%EC%9D%98-%EB%8F%99%EC%9E%91%EB%B0%A9%EC%8B%9D-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-1%ED%8E%B8
