---
date: 2024-05-04T23:11:59
updatedAt: 2024-09-22 00:01:09
tags:
  - JVM
  - Concurrency
  - hugo_blog
categories:
  - JVM
title: 4-Volitile
lastmod: 2024-09-21T15:01:26.576Z
---
```kotlin
fun main(){  
    repeat(3){  
        val worker = VolatileWorker() // worker 쓰레드 생성  
        worker.start() // worker 쓰레드 시작  
        Thread.sleep(100) // 메인 쓰레드 잠시 수면  
        println("stop을 true로 변경")  
        worker.stop = true // worker쓰레드의 stop 플래그 변경  
        worker.join() // worker 쓰레드가 끝날 때까지 메인쓰레드에서 대기  
    }  
    println("작업 종료")  
}  
  
class VolatileWorker : Thread() {  
    @Volatile  
    var stop = false  
    override fun run() {  
        super.run()  
        while(!stop){ }  
    }  
}  
  
class Worker : Thread() {  
    var stop = false  
    override fun run() {  
        super.run()  
        while(!stop){ }  
    }  
}
```

* 현재 실행되고 있는 프로그램의 변수의 값을 읽기 위해서는 main메모리 혹은 cpu캐시에서 데이터를 읽어오게 된다
* cpu캐시에 데이터가 있을 경우, main메모리가 아닌, cpu캐시에서 읽어오는 것이 더 효율적이기에 cpu캐시에서 데이터를 읽어온다
* 하지만 cpu캐시에서 계속 데이터를 읽어올경우, 다른 쓰레드에서 변경한 데이터를 읽지 않아 변경을 확인하지 못하는 문제가 발생할 수 있다
* 이때 변수에 @Volatile을 사용하면 해당 변수는 항상 main메모리에서만 데이터를 읽고 쓰기에 최신의 데이터에 대한 동작을 보장받을 수 있다
* 항상 main메모리에서 데이터를 읽어오기에 성능 저하가 발생한다

### Volatile을 사용하지 않았을때

![center|600](/image/real-resource-image/Pasted%20image%2020240226231655.png)

### Volatile을 사용할 때

![center|600](/image/real-resource-image/Pasted%20image%2020240226231714.png)

https://nesoy.github.io/articles/2018-06/Java-volatile\
https://jenkov.com/tutorials/java-concurrency/volatile.html
