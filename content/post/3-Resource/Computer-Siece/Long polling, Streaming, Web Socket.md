---
date: 2024-01-04T22:47:40
updatedAt: 2024-04-21 18:36:37+9980
tags:
  - Network
  - hugo_blog
categories:
  - Network
title: Long polling, Streaming, Web Socket
lastmod: 2024-04-21T09:36:37.998Z
---
## Why?

* 클라이언트가 서버에게 http요청을 보내는 상황이 아닌, 서버에서 클라이언트에게 요청을 보내는 상황이 발생시 유용함
  * 서버에서 작업이 즉시 처리되지 않고, 처리되기까지 대기해야하는 상황
  * 서버에서 어떤 이벤트 발생 시 클라이언트에게 알려야 하는 상황
* 이를 위한 방안으로 polling, Long polling, streaming, web socket, plugin 등의 방안이 존재

## 자세한 내용은 RFC파일을 참고

* 당장 사용할 사항이 아니고, 단지 궁금증에 알아보는 것이니  이 문서에서는 간단하게 요약

> Long polling과 streaming은 [RFC6202](https://datatracker.ietf.org/doc/html/rfc6202)참고\
> Web socket은 [RFC6455](https://datatracker.ietf.org/doc/html/rfc6455) 참고

## Pooling(폴링)

![center|400](/image/real-resource-image/Pasted%20image%2020240329225214.png)

* 클라이언트가 주기적으로 서버에 요청을 보내서 확인하는 방식
* 클라이언트가 주기적으로 서버에 요청을 보내므로 서버의 변경에 즉시 대응하지 못함
* 다른 방법들은 서버에서 커넥션을 유지하고 있어야 하는 반면 폴링 방식에서는 커넥션을 유지하지 않아도 돼 서버에 부하가 적음

## Long polling(롱폴링)

![center|600](/image/real-resource-image/Pasted%20image%2020240329225407.png)

* 클라이언트가 요청을 보내면, 서버에서 커넥션을 유지하다가 이벤트가 발생하면 응답하는 방식
* 폴링에 비해 실시간 처리가 가능함
* 서버가 여러번 응답을 해야 하는 상황에서는 효율이 좋지 않음
  * 서버가 응답을 한 후 클라이언트가 다시 요청해서 커넥션을 유지해야함
* Time-out 오류가 발생할 수 있음

## Streaming(스트리밍)

![center|600](/image/real-resource-image/Pasted%20image%2020240329230355.png)

* 클라이언트가 요청을 보내면, 서버에서는 커넥션을 유지하고 리스폰스를 보내는 방식
* 한번 응답을 보내고 커넥션이 끊어지는 것이 아니라 계속 응답을 보낼 수 있음
* HTTP/1.1 또는 HTTP/1.0에서 가능

> RFC문서에서는 위 두버전만 명시되어 있지만, 해당 문서는 2011년에 작성되었기에, 위 두 버전만 명시한것으로 보임\
> 그 이후버전에서도 가능할것이라 생각함

* HTTP의 EOF와 chunked transfer를 사용하여 연결을 지속함
  * HTTP/1.0에서는 EOF만 사용가능함
  * HTTP/1.1에서는 EOF와 chunked transfer 둘다 사용가능함

## Web Socket

![center|600](/image/real-resource-image/Pasted%20image%2020240329231640.png)

* 위의 방식들은 HTTP를 잘 활용해서 만든 방식이라면 Web Socket은 HTTP 프로토콜을 사용하지 않음
* 양방향으로 통신이 가능한 방식으로 언제든 서로 데이터를 보낼 수 있음

https://dydtjr1128.github.io/etc/2019/09/23/polling-long-polling-streaming.html\
https://bcho.tistory.com/896\
https://stackoverflow.com/questions/12555043/my-understanding-of-http-polling-long-polling-http-streaming-and-websockets\
https://sendbird.com/ko/developer/tutorials/websocket-vs-http-communication-protocols

https://datatracker.ietf.org/doc/html/rfc6202\
https://datatracker.ietf.org/doc/html/rfc6455
