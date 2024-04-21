---
date: 2023-10-03T23:00:14
updatedAt: 2024-04-21 18:34:36+3300
tags:
  - HTTP
  - hugo_blog
title: HTTP Method PUT vs PATCH
lastmod: 2024-04-21T09:36:38.178Z
---
PUT메소드는 현재 서버에 있는 대체하여 새로운 엔티티를 생성\
PATCH메소드는 현재 서버에 있는 엔티티를 수정하여 새버전을 생성하는것\
-> PUT메소드는 엔티티의 전체 변경 시, PATCH는 엔티티의 일부 변경 시 사용

> The difference between the PUT and PATCH requests is reflected in the\
> way the server processes the enclosed entity to modify the resource\
> identified by the Request-URI. In a PUT request, the enclosed entity\
> is considered to be a modified version of the resource stored on the\
> origin server, and the client is requesting that the stored version\
> be replaced. With PATCH, however, the enclosed entity contains a set\
> of instructions describing how a resource currently residing on the\
> origin server should be modified to produce a new version. The PATCH\
> method affects the resource identified by the Request-URI, and it\
> also MAY have side effects on other resources; i.e., new resources\
> may be created, or existing ones modified, by the application of a\
> PATCH.

<https://datatracker.ietf.org/doc/rfc5789/?include_text=1>

\#Concept
