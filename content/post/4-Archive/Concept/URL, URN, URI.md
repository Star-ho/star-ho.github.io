---
date: 2024-04-09T22:31:29
updatedAt: 2024-04-21 18:32:05+2360
tags:
  - Network
  - Concept
  - hugo_blog
categories:
  - Network
title: URL, URN, URI
lastmod: 2024-04-21T09:32:12.968Z
---
![|center|600](/image/real-resource-image/Pasted%20image%2020240409223209.png)

## URI(Uniform Resource Identifier)

* 통합 자원 식별자
* 인터넷에 있는 자원을 나타내는 유일한 주소
* URN과 URL을 포괄하는 개념
* URL은 URI가 될 수 없지만, URI는 URL이 될 수 있음
* *제한적인 URL과 URN보다는 URI라는 용어를 쓰자*
* 예시
  * data:text/plain;base64,SGVsbG8gV29ybGQh
  * data:image/jpeg;base64,/9j/4AAQSkZJRgAB…
  * tel:+1234567890
  * tel:555–123–4567
  * file:///path/to/file.txt
  * file:///C:/Documents/document.docx

## URL(Uniform Resource Locator)

* 통합 자원 위치
* 인터넷에서 웹페이지, 이미지, 비디오 등 리소스의 위치를 가르키는 문자열
* 자원를 식별하는것 뿐만아니라 수단을 제공
* 예시
  * https://www.example.com/index.html
  * http://api.example.com/data
  * ftp://ftp.example.com/files/document.pdf
  * info@example.com
  * mailto:user@example.com?subject=Hello\&body=Hi%20there

## URN(Uniform Resource Name)

* 통합 자원 이름
* 지속적이고, 위치에 독립적인 리소스 식별자
* 예시
  * urn:isbn:0–486–27557–4
  * urn:ietf:rfc:3986
  * urn:oid:2.16.840

https://datatracker.ietf.org/doc/html/rfc3986#section-1.1.3\
https://blog.bytebytego.com/i/132279282/url-uri-urn-do-you-know-the-differences\
https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-URL-URI-%EC%B0%A8%EC%9D%B4\
https://ko.wikipedia.org/wiki/%ED%86%B5%ED%95%A9\_%EC%9E%90%EC%9B%90\_%EC%8B%9D%EB%B3%84%EC%9E%90
