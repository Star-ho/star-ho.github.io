---
date: 2024-01-29T22:59:24
updatedAt: 2024-04-21 18:34:36+3190
tags:
  - Database
  - hugo_blog
categories:
  - Database
title: mysqldmup
lastmod: 2024-04-21T09:36:38.134Z
---
* dms로 운영->스테이지 디비 마이그레이션 하는데 인덱스가 사라져서 인덱스 추가를 위해 정보를 찾아보았다.
* 직접 index를 넣어주는 방법과 AWS Schema Conversion Tool(AWS SCT)를 사용하는게 있었음
* macOS를 따로 지원하지 않아 mysqldump를 사용하여 스키마를 설정하기로함

## 방법

* 5.7.mysql\_aurora.2.11.2 대상
  * mysql버전에 맞는 mysqldump가 필요
* 현재 로컬컴퓨터에 설치된 mysql버전과 대상 데이터베이스 버전이 다르다면 대상 데이터베이스 버전의 mysql-client 설치 필요

### 데이터 덤프

* 데이터를 가져올 데이터베이스

```
mysqldump  -u [유저명] -P [포트] -h [호스트명] --no-data -p --databases [스키마명] > schema.sql
```

### 데이터 복구

* 데이터를 넣을 데이터베이스

```
mysql -u [유저명] -p -P [포트번호] -h [호스트명] < schema.sql
```

```
ERROR 1227 (42000) at line 1715: Access denied; you need (at least one of) the SUPER privilege(s) for this operation
```

이런 에러가 날 수 있다\
해당 라인(예제에서는 1715라인)을 확인해서 삭제 or 주석처리 하거나 아래 링크를 참고하면 됨

> aws에서 제공하는 아래 가이드가 있는데 효과가 있지 않았음\
> https://aws.amazon.com/ko/premiumsupport/knowledge-center/error-1227-mysqldump/

### 에러에 대한 정보

* 에러가 나는 라인은 아래 3줄이다
* 언뜻 보면 세개의 변수에 특정값을 넣는건데 어떤 의미일까 찾아보았

```
SET @MYSQLDUMP_TEMP_LOG_BIN = @@SESSION.SQL_LOG_BIN;
SET @@SESSION.SQL_LOG_BIN= 0
SET @@GLOBAL.GTID_PURGED='';
```

```
SET @MYSQLDUMP_TEMP_LOG_BIN = @@SESSION.SQL_LOG_BIN;
SET @@SESSION.SQL_LOG_BIN= 0
```

* 먼저 이 두줄은 SESSION.SQL\_LOG\_BIN 변수를 수정하기 위한 옵션이다
* 첫번째줄은 원래 설정으로 돌리기 위해 SESSION.SQL\_LOG\_BIN값을 MYSQLDUMP\_TEMP\_LOG\_BIN에 임시로 저장한다
* 두번째줄이 핵심인데 SESSION.SQL\_LOG\_BIN를 0, false값으로 변경한다
* sql\_log\_bin은 현재 세션에서 바이너리 로그에 대한 로깅을 활성화할지 여부를 제어
* off로 설정시 현재 세션에서 바이너리 로그를 로깅하지 않음
  * 세션뿐만아니라 글로벌로 적용 가능
  * sql\_log\_bin값을 변경하려면 SUPER권한이 필요한데 없어서 에러가 발생하는

> GLOBAL.GTID\_PURGED는 선행지식이 좀 필요해보여 넘김
>
> * 시간날때 더 공부해보자

에러 참고 링크\
https://stackoverflow.com/questions/60320794/aws-rds-mysql-import-db-access-denied\
https://dev.mysql.com/doc/refman/8.0/en/set-sql-log-bin.html\
https://dev.mysql.com/doc/refman/8.0/en/replication-options-gtids.html#sysvar\_gtid\_purged

참고자료\
https://serverfault.com/questions/934879/copying-over-indexes-with-aws-dms\
https://www.lesstif.com/dbms/mysqldump-db-backup-load-17105804.html\
https://stackoverflow.com/questions/44015692/access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operat\
https://medium.com/@pushkarjoshi0410/solved-aws-rds-import-you-need-super-privilege-s-71e350b41989
