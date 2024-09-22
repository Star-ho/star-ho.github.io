---
date: 2024-03-04T22:41:04
updatedAt: 2024-09-22 16:07:50
tags:
  - Server-History
  - hugo_blog
  - Server
categories:
  - Server-History-Series
title: 3-Servlet Container
lastmod: 2024-09-22T07:07:50.708Z
---
* Servlet Engine이라고도 불림
* 서블릿을 실행하고 관리함
* 서블릿 메서드를 실행하고, 서블릿이 호출될때 필요한 서비스들을 제공함
* 서블릿에서 header나 parameter를 쉽게 접근할 수 있도록함
* 서블릿이 호출되면 웹서버는 http요청을 서블릿 컨테이너를 호출함
  * 컨테이너는 차례로 요청을 서블릿에 전달함

![center|600](/image/real-resource-image/Pasted%20image%2020231122230728.png)

* 요청이 들어오면 servlet conatiner는 다음과 같은 일련의 작업을 거침
  * 서블릿 인스턴스를 생성 후 init메서드를 호출하여 초기화함
  * 서블릿에 전달한 request객체를 구성함
    * http header, 클라이언트에서 전달된 파라미터, 서블릿 요청의 전체 URI
  * 서블릿에 대한 응답 객체를 생성함
  * 서블릿의 service메서드를 호출함
  * 적절한 상황에 destroy메서드를 호출해 서블릿이 garbage collector에 의해 폐기되도록 함
    * 성능상의 이유로 서블릿 컨테이너는 작업을 완료할때마다 서블릿을 파괴하지 않고, 메모리에 보관하는것이 일반적임
    * 웹서버 종료와 같은 드물게 발생하는 이벤트에 대해서만 소멸시킴

https://docs.oracle.com/cd/A97688\_16/generic.903/a97680/overview.htm
