---
date: 2024-04-07T12:24:58
updatedAt: 2024-04-21 18:32:05+2410
tags:
  - Trouble-Shooting
  - JVM
  - ORM
  - hugo_blog
categories:
  - Trouble-Shooting
title: Querydsl transform connection leak
lastmod: 2024-04-21T09:32:12.982Z
---
## 상황

* 현재 개발중인 기능에서 특정 api가 아래의 로그를 뱉으며 동작하지 않는 문제가 있다고 수정해달라는 요청을 받았다.

```
org.springframework.transaction.CannotCreateTransactionException: Could not open JPA EntityManager for transaction; nested exception is org.hibernate.exception.JDBCConnectionException: Unable to acquire JDBC Connection
<생략>
Caused by: java.sql.SQLTransientConnectionException: write-pool - Connection is not available, request timed out after 30000ms.
```

* write-pool에서 커넥션을 가져올 수 없다는 로그였다.
* 테스트 서버였고, 커넥션 10개로 설정되어있었다.
* 다른 업무도 있었고, 단순 커넥션 부족이라고 생각해서 테스트 서버를 재시작하였고, 커넥션 개수를 20개 까지 늘렸다

> 큰 착오였다, 테스트 서버에서 작업하는 인원은 5명이 채 되지 않았고, 절대 커넥션이 모자라지 않는 개수인데 당시에는 다른 작업으로 바빳고 대수롭지 않게 생각했었다

* 그 이후 2일뒤 커넥션 개수를 늘려도 계속 에러가 나서 수정요청을 받았다.
* 커넥션 수가 모자랄리가 없다고 판단했는데, 계속 에러가 난다고하여 우선 커넥션 관련 로그 설정을 하였다

```yml
logging:  
  level:  
    com.zaxxer.hikari.HikariConfig: DEBUG  
    com.zaxxer.hikari: TRACE  
    org.springframework.transaction.interceptor: TRACE  
```

* 위 로그를 설정하고, 서버 로그를 확인해보니, write-pool의 커넥션이 api요청이 끝난 후에도 반환되지 않는 것을 확인했다.

```
DEBUG 57394 --- [l-1 housekeeper] com.zaxxer.hikari.pool.HikariPool        : write-pool - Pool stats (total=20, active=0, idle=19, waiting=0)
DEBUG 57394 --- [l-1 housekeeper] com.zaxxer.hikari.pool.HikariPool        : write-pool - Pool stats (total=20, active=1, idle=18, waiting=0)
DEBUG 57394 --- [l-1 housekeeper] com.zaxxer.hikari.pool.HikariPool        : write-pool - Pool stats (total=20, active=2, idle=17, waiting=0)
```

* connection을 사용한 후  connection이 반환되지 않는 connection leak이 있는것을 확인하였고, hikariConnection leak을 확인 할 수 있는 설정을 추가하였다

```kotlin
dataSource.leakDetectionThreshold = 2000
```

> 커넥션이 누수를 판단하는 시간으로 디폴트로 0(disable)으로 설정됨

* 위 설정을 하니 아래의 로그가 남았다

```
java.lang.Exception: Apparent connection leak detected
```

* 로그에는 디테일한 메소드 정보까지 남았고, queryDsl의 trasform을 사용하는 메서드였다
  * @Transactional어노테이션 붙어있지 않았다!
* 관련해서 검색을 해보니, queryDsl의 transform을 @Transactional없이 사용하면, connection leak이 발생한다는 내용이 있었다.
* 우선 queryDsl의 transform를 사용하지 않는 로직에 대해 @Transactional을 다 붙여서 이슈를 종료하였다

## Deep dive!

```
테스트환경
spring boot 2.7.8
querydsl 5.0.0
```

* 왜 @Transcational이 붙지않는 querydsl의 transform에 connection leak이 발생했을까?
* 위 이유를 알기 위해 아래 2가지를 알아보려 한다
  * @Transcational이 붙을때 커넥션을 어떻게 반납하는지?
  * querydsl의 transform에서 커넥션 왜 반납하지 않는지?

### @Transcational이 붙을때 커넥션을 어떻게 반납하는지?

* 커넥션은 쿼리를 실행할때 얻음
  * Transactional을 실행할 때 얻지않음\
    -> 쿼리가 없는 메서드에 @Transactional을 붙여도 커넥션을 할당하지 않음

1. @Transactional이 있을떄
   * TransactionAspectSupport::createTransactionIfNecessary에서 txInfo를 리턴함
   * 반납은 transaction종료시 발생
   * 반납은 앞선 과정에서 받은 txInfo를  가지고, TransactionAspectSupport::commitTransactionAfterReturning의 인자로 넘겨 커넥션을 반납함
     * commitTransactionAfterReturning따라가다보면, ConcurrentBag::requite를 호출하는것을 확인함
     * txInfo내 entituManger에 connection에 대한 정보가 있음\
       -> @Transactional이 있으면 명시적으로 커넥션을 반환하는 로직이 있어서 @Transactional이 있다면 커넥션 반환이 정상적으로 이루어짐

2. @Transactional이 없을때
   * TransactionAspectSupport::createTransactionIfNecessary에서 txInfo가 없음
   * 트랜잭션 종료시 TransactionAspectSupport::commitTransactionAfterReturning에 넘길 txInfo가 없으므로 쿼리를 실행 후 커넥션을 반납하는 로직이 필요함
   * 아래의 로직으로 커넥션 반납함

```java
...

static{
	queryTerminatingMethods.add("execute");  
	queryTerminatingMethods.add("executeUpdate");  
	queryTerminatingMethods.add("getSingleResult");  
	queryTerminatingMethods.add("getResultStream");  
	queryTerminatingMethods.add("getResultList");  
	queryTerminatingMethods.add("list");
}
...
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
...
	if (SharedEntityManagerCreator.queryTerminatingMethods.contains(method.getName())) {  
	    if (this.outputParameters != null && this.target instanceof StoredProcedureQuery) {  
	        StoredProcedureQuery storedProc = (StoredProcedureQuery)this.target;  
	        Iterator var12 = this.outputParameters.entrySet().iterator();  
	  
	        while(var12.hasNext()) {  
	            Map.Entry<Object, Object> entry = (Map.Entry)var12.next();  
	  
	            try {  
	                Object key = entry.getKey();  
	                if (key instanceof Integer) {  
	                    entry.setValue(storedProc.getOutputParameterValue((Integer)key));  
	                } else {  
	                    entry.setValue(storedProc.getOutputParameterValue(key.toString()));  
	                }  
	            } catch (IllegalArgumentException var20) {  
	                IllegalArgumentException ex = var20;  
	                entry.setValue(ex);  
	            }  
	        }  
	    }  
	  
	    EntityManagerFactoryUtils.closeEntityManager(this.entityManager);  
	    this.entityManager = null;  
	}
...
```

* 실행한 메서드 명이 queryTerminatingMethods 안에 있을때, closeEntityManager를 호출함

### querydsl의 transform에서 커넥션 왜 반납하지 않는지?

* querydsl의 transform는 쿼리 조회시 scroll메서드를 사용함
  * queryTerminatingMethods에 존재하지 않음

## 정리

* @Transactional을 사용한다면 @Transactional 내부에 connection을 종료하는 로직이 있음
* @Transactional을 사용하지 않는다면, 커넥션이 끝날때, queryTerminatingMethods안에 존재하는 메서드를 사용해 커넥션을 종료해야함
* querydsl의 transform는 쿼리 조회시 scroll메서드를 사용하여 커넥션이 종료되지 않음

## 해당 이슈정리

* 해당 이슈는 2018년 4월부터 발생한 이슈이다.[github 이슈 링크](https://github.com/querydsl/querydsl/issues/2291)
* 해당 이슈가 테스트 환경인 spring boot 2.7.8에서도 재현되었고 혹시 아직 고쳐지지 않았나 싶어, spring-orm코드를 보니, 23년 11월 26일에 scroll을 추가 되어 있었다.
* 추가로 spring boot 3.0 버전부터는 JPAQueryFactory생성 자 JPQLTemplates.DEFAULT을 받도록 수정되어, 해당 이슈를 우회할 수 있는것으로 보인다.

\#Trouble-Shooting
