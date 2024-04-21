---
date: 2023-10-01T23:06:17
updatedAt: 2024-04-21 18:34:36+3290
tags:
  - JVM
  - Annotation
  - hugo_blog
categories:
  - JVM
title: Java built in Annotation
lastmod: 2024-04-21T09:36:38.176Z
---
java의 built in Annotation은\
**@Deprecated**\
**@Override**\
**@SuppressWarnings**\
**@SafeVarargs**\
**@FunctionalInterface**\
이 있고,

다른 어노테이션에 붙을 수 있는 어노테이션은\
**@Retention**\
**@Documented**\
**@Target**\
**@Inherited**\
**@Repeatable**\
이 있다

이 중 Retention의 정확한 의미가 와닿지 않아 정리해 보겠다.

# Retention

```
Indicates how long annotations with the annotated interface are to be retained
```

-> annotation이 얼마나 유지되는 정도를 나타냄, 디폴트는 Class

### Source

* 컴파일될때 사라짐

## Class

* 클래스파일에는 기록되지만, vm에 올라갈떄 사라짐
* 런타임시 리플렉션으로 정보를 가져올 수 없음

## Runtime

* 클래스파일에도 기록되고, vm에 올라감
* 런타임시 리플렉션으로 정보를 가져올 수 있음

> Source vs Class\
> Source는 컴파일된 바이트 코드에서 아예 보이지 않음\
> Class는 바이트코드에서 보이지만 invisible이라는 주석이 붙음\
> 바이트코드로 된 라이브러리를 만들때 사용

> Class vs Runtime\
> 런타임에 사용 가능 여부에 따라 다름\
> @Lorg/example/RetentionSourceAnnotation;() // invisible <- Class\
> @Lorg/example/RetentionSourceAnnotation;() <- Runtime\
> javaasist 라이브러로 두개다 확인가능\
> https://www.javassist.org/html/javassist/bytecode/AnnotationsAttribute.html

https://docs.oracle.com/javase/tutorial/java/annotations/predefined.html\
https://minkukjo.github.io/language/2020/09/30/Java-02/

\#Java
