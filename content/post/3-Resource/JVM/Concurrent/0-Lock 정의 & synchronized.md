---
date: 2024-04-01T22:38:42
updatedAt: 2024-09-22 15:57:04
tags:
  - JVM
  - Concurrency
  - hugo_blog
categories:
  - JVM
  - JVM/Concurrent
title: 0-Lock 정의 & synchronized
lastmod: 2024-09-22T06:57:04.289Z
---
# 사용하는 이유

### 쓰레드 동기화

* 멀티 쓰레드 환경에서 서로 다른 스레드가 하나의 자원을 공유해서 사용할때, 자원을 동시에 사용하면 예상치 않은 동작이 발생
  * 쓰레드 간섭(Thread Interference), 메모리 일관성 오류(Memory consistency Error)
  * ex) 두개의 쓰레드가 하나의 변수에 10을 증가시키려할때, 20이 증가되지 않고 10이 증가되는 문제
* 위와 같은 방법을 해결하기 위해 쓰레드를 동기화 하여 하나의 자원을 동시에 사용하지 못하는 방법

# 종류 및 사용방법

## Synchronized

* synchronized는 내재적 잠금(Intrinsic Lock) 또는 모니터 잠금(Monitor Lock)으로 알려진 내부 앤티티 중심으로 구현됨
  * 내재적 잠금은 동기화의 두가지 측면을 구현함
    * 객체의 상태에 대한 배타적 엑세스를 강제함
    * 가시성에 필수적인 happens-before relationships(발생 전 관계)를 설정

* java의 모든 Object은 내재적 잠금을 가지고 있음
  * header에 들어가있음

* 객체의 필드에 독점적이고 일관된 엑세스를 필요로 하는 쓰레드는, 객체를 접근하기전객체의 내재적 잠금을 얻어야함
  * 그리고 작업이 끝나면 내재적 잠금을 해제(release)해야함

* 쓰레드는 잠금을 획득한 시점부터 해제할때까지 내재적 잠금을 소유해야함

* 한 스레드가 내재적 잠금을 소유하고 있으면, 다른 쓰레드는 같은 lock을 소유할 수 없음
  * 다른 쓰레드가 잠금을 획득하려하면 block됨

* 스레드가 내재적 잠금을 해제하면, 해당 작업과 이후 동일한 잠금을 획득하는 모든 작업간에 선후(happens-before) 관계가 설정됨

* java에서는 synchronized method와 synchronized statment를 제공함

### synchronized method

* 스레드가 synchronized 메서드를 호출하면, 해당 메서드의 객체에 대한 내재 잠금을 자동으로 획득하고 메서드가 반환될때 잠금을 해제
  * 반환이 잡히지 않은 예외로 인해 발생한 경우에도 잠금이 해제됨
  * 잠금이 객체단위에 적용되므로 increment뿐만 아닌 increment와 decrement도 동시에 동작하지 않음
* 정적 synchronized 메서드가 호출되면 객체가 아닌 클래스에 대한 내재 잠금을 획득

```kotlin
class SynchronizedCounter {  
    private var c = 0  
  
    @Synchronized  
    fun increment() {  
        c++  
    }  
  
    @Synchronized  
    fun decrement() {  
        c--  
    }  
  
    @Synchronized  
    fun value(): Int {  
        return c  
    }  
}
```

* synchronized 메소드를 사용하면 아래 2가지 효과를 얻을 수 있음
  * 동일한 객체에 대해 synchronized 메서드 호출이 동시에 발생되지 않음
    * 한 스레드가 객체에 대해 synchronized 메서드를 실행하면, 첫번째 쓰레드가 작업을 완료할때까지 동기화된 메서드를 호출하는 다른 모든 쓰레드가 block됨
  * synchronized된 메서드가 종료되면, 동일한 객체에 대한 synchronized 메서드의 후속  호출과 함께 happends-before 관계가 설립됨
    * 이로인해 모든 스레드에서 해당 객체 변경사항을 확인할 수 있음
* 생성자 메소드에는 synchronized를 호출할 수 없음

### Synchronized statements

* synchronized method와 다르게 명시적으로 객체를 정의해야함
* 명시적의로 정의한 객체의 잠금을 획득하고 반환할때 잠금을 해제함

```java
public void addName(String name) {
    synchronized(this) {
        lastName = name;
        nameCount++;
    }
    nameList.add(name);
}
```

* 위의 예시에서는 lastName과 nameCount에는 동기화가 필요하지만, nameList에는 동기화가 필요하지 않은 경우이다

* Synchronized statements가 없다면 nameList.add를 호출하는 별도의 메서드가 필요함

* 세분화된 동기화시에도 Synchronized statements가 유용함

```java
public class MsLunch {
    private long c1 = 0;
    private long c2 = 0;
    private Object lock1 = new Object();
    private Object lock2 = new Object();

    public void inc1() {
        synchronized(lock1) {
            c1++;
        }
    }

    public void inc2() {
        synchronized(lock2) {
            c2++;
        }
    }
}
```

* c1과 c2는 절대 함께 사용되지 않으며, 이런 필드의 모든 업데이트는 동기화 되어야함
  * 하지만 c1과 c2사이의 동기화는 고려하지도 않아도 됨
* 위와 같은상황에서 synchronized statements가 유용함

### Synchronized method or statements에서만 사용가능한 메서드

* Synchronized method or statements외에 사용시 IllegalMonitorStateException 오류 발생

* wait
  * 현재 쓰레드를 중단하고 객체 잠금을 해제함
  * notify() 또는 notifyAll메서드를 호출하여 해당 객체를 알릴때까지 대기함

* notify
  * 객체 잠금을 가진 한 쓰레드르 깨우고 실행함
  * 깨어난 쓰레드는 객체 잠금을 얻고 작업을 수행
  * 어떤 쓰레드를 깨울지는 알 수 없기에 notifyAll을 주로 사용

* notifyAll
  * 객체 잠금을 기다리는 모든 스레드르 꺠우고 실행
  * 깨어난 쓰레드들은 객체 잠금을 얻기위해 경쟁함

https://www.ibm.com/docs/en/i/7.3?topic=techniques-synchronization-among-threads\
https://www.linkedin.com/pulse/thread-synchronization-techniques-ensuring-order-concurrent-n/\
https://docs.oracle.com/javase/tutorial/essential/concurrency/syncmeth.html
