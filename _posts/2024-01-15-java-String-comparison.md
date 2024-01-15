---
layout: post
title: Java 문자열 비교
categories: Java
date: 2024-01-15 23:24:00 +0900
---
자바에서 문자열은 어떻게 비교하는가?

다음 코드를 보자.

```java
String a = "Hello";
String b = "Hello";
String c = new String("Hello");

System.out.println(a == b);
System.out.println(a == c);
System.out.println(a.equals(c));
```

위 코드의 출력 결과는 다음과 같다.

```
true
false
true
```

<br>

<b>문자열에서 '==', '!='는 참조값 비교이고, '.equals()'는 값 비교이다.</b>

자바에서 ```new String("~~~")```은 새로운 메모리 공간에 만든다는 것이다.

즉, <u>자바에서 문자열 비교는 ```.equals()```로 해야 한다!</u>