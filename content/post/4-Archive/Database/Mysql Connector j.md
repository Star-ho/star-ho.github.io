---
date: 2024-04-01T23:04:47
updatedAt: 2024-04-21 18:32:05+2370
tags:
  - JVM
  - MySQL
  - hugo_blog
categories:
  - Database
title: Mysql Connector j
lastmod: 2024-04-21T09:32:12.847Z
---
* Java를 사용하는 어플리케이션에 mysql의 연결을 쉽게 하기 위해 개발됨

* JDBC type 4 driver이며, JDBC 4.2 specification을 구현함

* mysql connector-j를 사용해서 커넥션을 얻고, 구문을 실행하여 5번째 컬럼의 문자열 결과를 얻어오는 코드는 아래와 같음

```kotlin
DriverManager.getConnection("jdbc:mysql://localhost:3306/dreamStore","root","tjdgh123").use {conn ->  
    conn.createStatement().use { statment ->  
        val resultSet = statment.executeQuery("select * from product")  
        while(resultSet.next()){  
            println(resultSet.getString(5))  
        }  
    }  
}
```

* Connection을 한번 생성하면 데이터베이스에서 메타데이터를 가져오기 위한, Statement객체와 PrepareStatement객체를 생성하는데 사용 가능함
  * select쿼리는 executeQuery(String)메서드를 사용해서 호출가능
  * insert, update, delete쿼리는 executeUpdate(String)메서드를 사용해서 호출가능
  * 만약 쿼리가 조회 쿼리인지, 변경 쿼리인지 알 수 없다면, execute(String)을 사용하여 확인가능
    * 데이터 조회 쿼리라면 true, 변경쿼리라면 false를 리턴함
