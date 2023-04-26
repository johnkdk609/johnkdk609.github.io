---
layout: post
title: 예외 발생시키기 throw와 checked 예외, unchecked 예외
description: Java의 정석 기초편 ch. 8 예외처리
categories: Java
date: 2023-04-26 16:10:00 +09:00
---
키워드 **throw**를 사용해서 프로그래머가 고의로 예외를 발생시킬 수 있으며, 방법은 아래의 순서를 따르면 된다.

> **1. 연산자 new를 이용해서 발생시키려는 예외 클래스의 객체를 만든 다음**\
>   Exception e = new Exception("고의로 발생시켰음");
>
> **2. 키워드 throw를 이용해서 예외를 발생시킨다.**\
>   throw e;

```java
class Ex8_6 {
	public static void main(String args[]) {
		try {
			Exception e = new Exception("고의로 발생시켰음.");
			throw e;	 // 예외를 발생시킴 
                    //  throw new Exception("고의로 발생시켰음.");	// 위의 두 줄을 이렇게 한 줄로 줄여쓸 수 있다.

		} catch (Exception e) {
			System.out.println("에러 메시지 : " + e.getMessage());
			e.printStackTrace();
		}
		System.out.println("프로그램이 정상 종료되었음.");
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
에러 메시지 : 고의로 발생시켰음.
java.lang.Exception: 고의로 발생시켰음.
	at Ex8_6.main(Ex8_6.java:4)
프로그램이 정상 종료되었음.
```

Exception 인스턴스를 생성할 때, 생성자에 String을 넣어주면, 이 String이 Exception 인스턴스에 메시지로 지정된다. 이 메시지는 getMessage()를 이용해서 얻을 수 있다.


### checked 예외, unchecked 예외

```java
class Ex8_7 {
	public static void main(String[] args) {
		throw new Exception();		// Exception을 고의로 발생시킨다. 
	}
}
```

위 코드의 컴파일 결과는 다음과 같다.

```
Exception in thread "main" java.lang.Error: Unresolved compilation problem: 
	Unhandled exception type Exception

	at Ex8_7.main(Ex8_7.java:3)
```

이 예제를 작성한 후에 컴파일 하면, 위와 같은 에러가 발생하며 컴파일이 완료되지 않는다. 예외처리가 되어 있지 않다는 에러이다. 위의 결과에서 알 수 있는 것처럼, '**Exception 클래스와 그 자손들 (checked 예외)'이 발생할 가능성이 있는 문장들에 대해 예외처리를 해주지 않으면 컴파일조차 되지 않는다.** 

```java
class Ex8_8 {
	public static void main(String[] args) {
		throw new RuntimeException();	// RuntimeException을 고의로 발생시킨다. 
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Exception in thread "main" java.lang.RuntimeException
	at Ex8_8.main(Ex8_8.java:3)
```

이 예제는 예외처리를 하지 않았음에도 불구하고 이전의 예제(Ex8_8)와는 달리 성공적으로 컴파일 된다. 그러나 실행하면, 위의 실행 결과처럼 RuntimeException이 발생하여 비정상적으로 종료된다. 이 예제가 명백히 RuntimeException을 발생시키는 코드를 가지고 있고, 이에 대한 예외처리를 하지 않았음에도 불구하고 성공적으로 컴파일 되었다. 

'**RuntimeException 클래스와 그 자손 (unchecked 예외)**'에 해당하는 예외는 프로그래머의 실수로 발생하는 것들이기 때문에 예외처리를 강제하지 않는 것이다. 만일 RuntimeException 클래스들에 속하는 예외가 발생할 가능성이 있는 코드에도 예외처리를 해야 한다면, 아래와 같이 참조변수와 배열이 사용되는 모든 곳에 예외처리를 해주어야 할 것이다. 

```java
try {
  int[] arr = new int[10];
  System.out.println(arr[0]);
} catch (IndexOutOfBoundsException ie) {
  // ...
} catch (NullPointerException ne) {
  // ...
}
```
