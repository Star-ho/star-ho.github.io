---
date: 2024-01-28T13:54:21
updatedAt: 2024-04-21 18:34:36+2790
tags:
  - JVM
  - Spring
  - hugo_blog
categories:
  - Spring
title: Servlet에서 handler와 interceptor가 적용되는 과정
lastmod: 2024-04-21T09:36:38.083Z
---
Spring handler에 대해 공부하던 중, 실제로 어떻게 동작하는지 궁금하여 알아보았습니다.

DispatcherServlet의 doDispatch는 요청을 핸들러로 실제로 디스패치 하는 메서드입니다\
먼저 request로 getHandler메서드에서 매핑된 핸들러를 가져옵니다

> 이후 해당 핸들러를 사용하여 HandlerAdaptor를 들거와서 핸들링합니다

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {  
    HttpServletRequest processedRequest = request;  
    HandlerExecutionChain mappedHandler = null;  
    boolean multipartRequestParsed = false;  
  
    WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);  
  
    try {  
       ModelAndView mv = null;  
       Exception dispatchException = null;  
  
       try {  
          processedRequest = checkMultipart(request);  
          multipartRequestParsed = (processedRequest != request);  
  
          // Determine handler for the current request.  
          mappedHandler = getHandler(processedRequest);
          if (mappedHandler == null) {  
             noHandlerFound(processedRequest, response);  
             return;  
          }  
  
          // Determine handler adapter for the current request.  
          HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());  
  
...
  
          // Actually invoke the handler.  
          mv = ha.handle(processedRequest, response, mappedHandler.getHandler());  
  
          if (asyncManager.isConcurrentHandlingStarted()) {  
             return;  
          }  
  
          applyDefaultViewName(processedRequest, mv);  
          mappedHandler.applyPostHandle(processedRequest, response, mv);  
       ...
}
```

getHander메서드에서는 현재 인스턴스의 handlerMappings에 존재하는 mapping에서 현재 request를 인자로 getHandler요청을 호출합니다\
mapping의 getHandler는 AbstractHandlerMapping의 getHandler를 사용합니다

```java
protected HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {  
    if (this.handlerMappings != null) {  
       for (HandlerMapping mapping : this.handlerMappings) {  
          HandlerExecutionChain handler = mapping.getHandler(request);  
          if (handler != null) {  
             return handler;  
          }  
       }  
    }  
    return null;  
}
```

AbstractHandlerMapping의 getHandler는 getHandlerInternal을 호출하여 handler를 가져옵니다

```java
public final HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {  
    Object handler = getHandlerInternal(request);  
    if (handler == null) {  
       handler = getDefaultHandler();  
    }  
    if (handler == null) {  
       return null;  
    }  
    // Bean name or resolved handler?  
    if (handler instanceof String) {  
       String handlerName = (String) handler;  
       handler = obtainApplicationContext().getBean(handlerName);  
    }  
  
    // Ensure presence of cached lookupPath for interceptors and others  
    if (!ServletRequestPathUtils.hasCachedPath(request)) {  
       initLookupPath(request);  
    }  
  
    HandlerExecutionChain executionChain = getHandlerExecutionChain(handler, request);  
  .....
  
    return executionChain;  
}
```

getHandlerInternal에서는 request를 확인하여 lookupPath를 만들고, lookupHandlerMethod메서드를 통해 HandlerMethod를 가져옵니다

```java
protected HandlerMethod getHandlerInternal(HttpServletRequest request) throws Exception {  
    String lookupPath = initLookupPath(request);  
    this.mappingRegistry.acquireReadLock();  
    try {  
       HandlerMethod handlerMethod = lookupHandlerMethod(lookupPath, request);  
       return (handlerMethod != null ? handlerMethod.createWithResolvedBean() : null);  
    }  
    finally {  
       this.mappingRegistry.releaseReadLock();  
    }  
}
```

path와 request를 가지고 HandlerMethod를 확인합니다.

* getMappingsByDirectPath메서드를 사용하여 path와 직접 되는 매핑을 확인하고 matches list에 넣습니다
  * 없다면 mapping registry에서 모든 매핑정보를 가져와 확인합니다.
* 매핑후 matches list의 개수에 따라
  * 1개를 초과한다면정렬을 하고, 첫번째와 두번째 mapping의 우선순위가 없다면 IllegalStateException를 리턴합니다
  * 없다면 null을 리턴합니다
* 1개만 존재한다면 매칭된 핸들러 메소드를 리턴합니다

```java
protected HandlerMethod lookupHandlerMethod(String lookupPath, HttpServletRequest request) throws Exception {  
    List<Match> matches = new ArrayList<>();  
    List<T> directPathMatches = this.mappingRegistry.getMappingsByDirectPath(lookupPath);  
    if (directPathMatches != null) {  
       addMatchingMappings(directPathMatches, matches, request);  
    }  
    if (matches.isEmpty()) {  
       addMatchingMappings(this.mappingRegistry.getRegistrations().keySet(), matches, request);  
    }  
    if (!matches.isEmpty()) {  
       Match bestMatch = matches.get(0);  
       if (matches.size() > 1) {  
          Comparator<Match> comparator = new MatchComparator(getMappingComparator(request));  
          matches.sort(comparator);  
          bestMatch = matches.get(0);  
          
 ...
 
          else {  
             Match secondBestMatch = matches.get(1);  
             if (comparator.compare(bestMatch, secondBestMatch) == 0) {  
                Method m1 = bestMatch.getHandlerMethod().getMethod();  
                Method m2 = secondBestMatch.getHandlerMethod().getMethod();  
                String uri = request.getRequestURI();  
                throw new IllegalStateException(  
                      "Ambiguous handler methods mapped for '" + uri + "': {" + m1 + ", " + m2 + "}");  
             }  
          }  
       }  
       request.setAttribute(BEST_MATCHING_HANDLER_ATTRIBUTE, bestMatch.getHandlerMethod());  
       handleMatch(bestMatch.mapping, lookupPath, request);  
       return bestMatch.getHandlerMethod();  
    }  
    else {  
       return handleNoMatch(this.mappingRegistry.getRegistrations().keySet(), lookupPath, request);  
    }  
}
```

addMatchingMappings을 따라가다보면 getMatchingCondition메서드를 만납니다

* 메서드, 파라미터, 헤더, path 등을 확인하며 RequestMappingInfo를 리턴합니다

```java
public RequestMappingInfo getMatchingCondition(HttpServletRequest request) {  
    RequestMethodsRequestCondition methods = this.methodsCondition.getMatchingCondition(request);  
    if (methods == null) {  
       return null;  
    }  
    ParamsRequestCondition params = this.paramsCondition.getMatchingCondition(request);  
    if (params == null) {  
       return null;  
    }  
    
	....
	
    RequestConditionHolder custom = this.customConditionHolder.getMatchingCondition(request);  
    if (custom == null) {  
       return null;  
    }  
    return new RequestMappingInfo(this.name, pathPatterns, patterns,  
          methods, params, headers, consumes, produces, custom, this.options);  
}
```

getHandlerInternal에서 가져온 handlerMethod를 인자로 getHandlerExecutionChain호출하는데 HandlerExecutionChain에서 interceptor를 적용합니다

````java
public final HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {  
    Object handler = getHandlerInternal(request);  
    if (handler == null) {  
       handler = getDefaultHandler();  
    }  
    if (handler == null) {  
       return null;  
    }  
    // Bean name or resolved handler?  
    if (handler instanceof String) {  
       String handlerName = (String) handler;  
       handler = obtainApplicationContext().getBean(handlerName);  
    }  
  
    // Ensure presence of cached lookupPath for interceptors and others  
    if (!ServletRequestPathUtils.hasCachedPath(request)) {  
       initLookupPath(request);  
    }  
  
    HandlerExecutionChain executionChain = getHandlerExecutionChain(handler, request);  
  .....
  
    return executionChain;  
}```

HandlerExecutionChain메서드
```java 
	protected HandlerExecutionChain getHandlerExecutionChain(Object handler, HttpServletRequest request) {  
	    HandlerExecutionChain chain = (handler instanceof HandlerExecutionChain ?  
	          (HandlerExecutionChain) handler : new HandlerExecutionChain(handler));  
	  
	    for (HandlerInterceptor interceptor : this.adaptedInterceptors) {  
	       if (interceptor instanceof MappedInterceptor) {  
	          MappedInterceptor mappedInterceptor = (MappedInterceptor) interceptor;  
	          if (mappedInterceptor.matches(request)) {  
	             chain.addInterceptor(mappedInterceptor.getInterceptor());  
	          }  
	       }  
	       else {  
	          chain.addInterceptor(interceptor);  
	       }  
	    }  
	    return chain;  
	}
````
