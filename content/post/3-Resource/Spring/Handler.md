---
date: 2024-01-28T10:42:53
updatedAt: 2024-04-21 18:34:36+2780
tags:
  - Spring
  - Concept
  - hugo_blog
categories:
  - Spring
title: Handler
lastmod: 2024-04-21T09:36:38.078Z
---
먼저 Handler란, 특정 유형의 데이터에 특화되어 있거나, 특정한 작업에 초점에 맞춘 루틴/함수/메서드입니다.

스프링에서 handlerAdaptor를 제공하는데, handlerAdaptor는 HTTP요청을 쉽게 처리할 수 있는 인터페이스입니다.

HandlerMapping으로 특정 URL에 매핑됩니다.

DispatcherServlet은 handlerAdaptor를 통해 Handler를 실행하고, 이로인해 DispatcherServlet과 Handler는 느슨한 연결을 유지할 수 있습니다.

```
public interface HandlerAdapter { 
	boolean supports(Object handler); 
	ModelAndView handle( 
		HttpServletRequest request, 
		HttpServletResponse response, 
		Object handler) throws Exception; 
	
	long getLastModified(HttpServletRequest request, Object handler); 
}
```

support메서드는 handle메서드를 호출하기 전, 인자로 받은 handler를 처리할 수 있는지 여부를 판단합니다

handle메서드는  실제로 HTTP요청을 처리하는 부분입니다.

getLastModified는 Deprecated되었습니다

https://stackoverflow.com/questions/195357/what-is-a-handler\
https://www.baeldung.com/spring-mvc-handler-adapters\
https://www.baeldung.com/spring-handler-mappings\
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/HandlerAdapter.html#getLastModified(jakarta.servlet.http.HttpServletRequest,java.lang.Object)
