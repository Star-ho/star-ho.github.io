---
date: 2024-03-02T22:41:32
updatedAt: 2024-04-21 18:34:34+8650
tags:
  - Server-History
  - hugo_blog
categories: Server-History
title: 4-Jakarta Servlet
lastmod: 2024-04-21T09:36:36.372Z
---
* 이전의 이름음 java servlet임
  * eclipse재단과 oracle과의 상표권 분쟁으로 jakarta로 변경됨 [링크](https://www.samsungsds.com/kr/insights/java_jakarta.html)
* HTTP 요청과 응답을 처리하기 위한 서버측 api를 정의한것 [링크](https://jakarta.ee/specifications/servlet/)
* 요청을 수신하고 요청에 따라 응답을 생성하는 객체에 대한 정의
* [요청과 관련된 3가지의 메소드가 존재](https://jakarta.ee/specifications/platform/9/apidocs/jakarta/servlet/servlet)
  * init
    * 서블릿이 서비스에 배치되었다는것을 알리기 위해 서블릿컨테이너에서 호출
    * 서블릿 컨테이너는 서블릿을 인스턴스 한 후 정확히 한번만 호출함
    * 서블릿이 요청을 정상적으로 처리하기 위해서는 초기화가 성공적으로 완료되어야함
  * service
    * 반드시 init 메소드가 호출된 이후에 호출됨
    * 서블릿 컨테이너에서 호출하여 서블릿이 요청에 응답할 수 잇도록함
    * 오류를 던지거나 전송하는 서블릿은 항상 상태코드를 전송해야함
    * 서블릿은 일반적으로 여러 요청을 동시에 처리할 수 있는 멀티 스레드 컨테이너 내에서 실행됨
      * 공유리소스에 대한 동기화에 대해 고려해야함
  * destroy
    * 서블릿 컨테이너에서 호출하며, 서비스가 중단중임을 서블릿에 알림
    * 서블릿의 서비스 메서드 내의 모든 쓰레드가 종료되거나 시간초과가 지난 후에 호출됨
    * destroy가 호출된 이후에는 service가 다시 호출되지 않음
