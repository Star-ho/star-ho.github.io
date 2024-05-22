---
date: 2024-01-27T23:13:55
updatedAt: 2024-04-21 18:34:36+2780
tags:
  - Spring
  - Concept
  - hugo_blog
categories:
  - Spring
title: Interceptor
lastmod: 2024-04-21T09:36:38.073Z
---
handlerMapping의 구현으로 특정요청에 대해 특정 기능을 적용할때 사용합니다\
아래 3가지 메소드가 존재합니다

### preHandle

* 핸들러가 실행되기 전 실행됩니다
* boolean을 리턴하는데, true를 리턴하면 이후 실행이 계속되지만, false나 예외를 리턴하면 이후의 interceptor 및 핸들러를 실행하지 않습니다

### postHandle

* 핸들러가 실행된 후 실행됩니다

### afterCompletion

* 모든 완료된 후 실행됩니다

## vs Filter

* Filter와의 차이점으로 Filter는 Dispatcher Servlet이 실행되기전 적용됩니다
* Interceptor는 Dispatcher Servlet이 실행된 후 호출이 됩니다
* Filter는 전체 수명주기를 대상으로하는 작업에 적합함

> deep-dive\
> https://eclipse-ee4j.github.io/jersey.github.io/documentation/latest/filters-and-interceptors.html

> webflux에서는 webfilter가 동일한 기능을 제공함

https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/handlermapping-interceptor.html\
https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/interceptors.html\
https://gngsn.tistory.com/153\
https://docs.spring.io/spring-framework/reference/web/webflux/reactive-spring.html#webflux-filters\
https://stackoverflow.com/questions/35856454/difference-between-interceptor-and-filter-in-spring-mvc

\#Spring
