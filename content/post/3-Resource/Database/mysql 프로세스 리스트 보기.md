---
date: 2023-10-06T23:30:28
updatedAt: 2024-04-21 18:34:36+3200
tags:
  - Database
  - hugo_blog
  - Mysql-Trouble-shooting
categories:
  - Database
title: mysql 프로세스 리스트 보기
lastmod: 2024-04-21T09:36:38.139Z
---
select \* from information\_schema.PROCESSLIST;

![Pasted image 20231007162016](/image/real-resource-image/Pasted%20image%2020231007162016.png)

### ID

* connection의 식별자

### USER

* 해당 구문을 실행한 유저
* event\_schduler는 예약된 이벤트를 모니터링하는 스레드

### HOST

* 해당 구문을 실행한 호스트명

### DB

* 해당 스레드가 선택한 데이터베이스

### COMMAND

* 실행하는 명령의 유형
* sleep - 클라이언트에게 새로운 구문을 받기위해 대기하는 상태
* query - 쿼리를 실행중인 상태
* [상태 참고 링크](https://dev.mysql.com/doc/refman/8.0/en/thread-commands.html)

## TIME

* 쓰레드가 현재 상태에 있던 시간
* 초단위

### STATE

* 스레드가 수행 중인 작업, 이벤트의 상태입니다
* [참고 링크](https://dev.mysql.com/doc/refman/8.0/en/general-thread-states.html)

### INFO

* 쓰레드가 실행하고있는 구문을 보여줌
* nll일 때는 구문을 실행하고 있지 않다는것

https://dev.mysql.com/doc/refman/8.0/en/information-schema-processlist-table.html

\#Database\
\#Query\
\#Trouble-Shooting
