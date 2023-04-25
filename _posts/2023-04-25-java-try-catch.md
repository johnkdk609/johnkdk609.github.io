---
layout: post
title: 예외 처리하기 - try-catch문
description: Java의 정석 기초편 ch. 8 예외처리
categories: Java
date: 2023-04-25 12:11:00 +09:00
---
프로그램의 실행 도중에 발생하는 에러는 어쩔 수 없지만, 예외는 프로그래머가 이에 대한 처리를 미리 해줘야 한다.

예외처리(exception handling)란, 프로그램 실행 시 발생할 수 있는 예기치 못한 예외의 발생에 대비한 코드를 작성하는 것이며, 예외처리의 목적은 예외의 발생으로 인한 실행 중인 프로그램의 갑작스런 비정상 종료를 막고, 정상적인 실행 상태를 유지할 수 있도록 하는 것이다. 

> **예외처리(exception handling)의**\
> **정의** - 프로그램 실행 시 발생할 수 있는 예외의 발생에 대비한 코드를 작성하는 것\
> **목적** - 프로그램의 비정상 종료를 막고, 정상적인 실행 상태를 유지하는 것

(에러와 예외는 모두 실행 시(runtime) 발생하는 오류이다.)

발생한 예외를 처리하지 못하면, 프로그램은 비정상적으로 종료되며, 처리되지 못한 예외(uncaught exception)는 JVM의 '예외처리기(UncaughtExceptionHandler)'가 받아서 예외의 원인을 화면에 출력한다.

예외를 처리하기 위해서는 **try-catch문**을 사용하며, 그 구조는 다음과 같다.

```java
try {
  // 예외가 발생할 가능성이 있는 문장들을 넣는다.
} catch (Exception1 e1) {
  // Exception1이 발생했을 경우, 이를 처리하기 위한 문장을 적는다.
} catch (Exception2 e2) {
  // Exception2가 발생했을 경우, 이를 처리하기 위한 문장을 적는다.
} catch (ExceptionN eN) {
  // ExceptionN이 발생했을 경우, 이를 처리하기 위한 문장을 적는다. 
}
```

하나의 try블럭 다음에는 여러 종류의 예외를 처리할 수 있도록 하나 이상의 catch블럭이 올 수 있으며, 이 중 발생한 예외의 종류와 일치하는 단 한 개의 catch블럭만 수행된다. 발생한 예외의 종류와 일치하는 catch블럭이 없으면 예외는 처리되지 않는다. 

(if문과 달리, try블럭이나 catch블럭 내에 포함된 문장이 하나뿐이어도 괄호{}를 생략할 수 없다.)


### try-catch문에서의 흐름

try-catch문에서, 예외가 발생한 경우와 발생하지 않았을 때 흐름이 달라진다.

> **try블럭 내에서 예외가 발생한 경우,**
> 1. 발생한 예외와 일치하는 catch블럭이 있는지 확인한다.
> 2. 일치하는 catch블럭을 찾게 되면, 그 catch블럭 내의 문장들을 수행하고 전체 try-catch문을 빠져나가서 그 다음 문장을 계속해서 수행한다. 일치하는 catch블럭을 찾지 못하면, 예외는 처리되지 못한다. 
> 
> **try블럭 내에서 예외가 발생하지 않은 경우,**
> 1. catch블럭을 거치지 않고 전체 try-catch문을 빠져나가서 수행을 계속한다.

```java
class Ex8_1 {
	public static void main(String args[]) {
		System.out.println(1);			
		try {
			System.out.println(2);
			System.out.println(3);
		} catch (Exception e) {
			System.out.println(4);  // 실행되지 않는다. 
		} // try-catch의 끝 
		System.out.println(5);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
1
2
3
5
```

위의 예제에서는 예외가 발생하지 않았으므로 catch블럭의 문장이 실행되지 않았다. 다음의 예제는 위의 예제를 변경해서, try블럭에서 예외가 발생하도록 하였다.

```java
class Ex8_2 {
	public static void main(String args[]) {
		System.out.println(1);
		try {
			System.out.println(0/0);  // 0으로 나눠서 고의로 ArithmeticException을 발생시킨다.
			System.out.println(2); 	// 실행되지 않는다. 
		} catch (ArithmeticException ae) {
			System.out.println(3);
		} // try-catch의 끝 
		System.out.println(4);
	} // main 메서드의 끝 
}
```

위 코드의 출력 결과는 다음과 같다.

```
1
3
4
```

위의 결과를 보면, 1을 출력한 다음 try블럭에서 예외가 발생했기 때문에 try블럭을 바로 벗어나서 2는 출력되지 않는다. 그리고는 예외에 해당하는 catch블럭으로 이동하여 문장들을 수행한 후에 전체 try-catch문을 벗어나서 그 다음 문장을 실행하여 4를 출력한다. 


### 예외의 발생과 catch블럭

catch블럭은 괄호()와 블럭{} 두 부분으로 나눠져 있는데, 괄호() 내에는 처리하고자 하는 예외와 같은 타입의 참조변수 하나를 선언해야 한다.

예외가 발생하면, 발생한 예외에 해당하는 클래스의 인스턴스가 만들어진다. 예제8-2에서는 ArithmeticException이 발생했으므로 ArithmeticException 인스턴스가 생성된다. 예외가 발생한 문장이 try블럭에 포함되어 있다면, 이 예외를 처리할 수 있는 catch블럭이 있는지 찾게 된다.

첫 번째 catch블럭부터 차례로 내려가면서 catch블럭의 괄호() 내에 선언된 참조변수의 종류와 생성된 예외클래스의 인스턴스에 **instanceof 연산자를 이용해서 검사**하게 되는데, 검사 결과가 true인 catch블럭을 만날 때까지 검사는 계속된다. 

검사 결과가 true인 catch블럭을 찾게 되면 블럭에 있는 문장들을 모두 수행한 후에 try-catch문을 빠져나가고 예외는 처리되지만, 검사 결과가 true인 catch블럭이 하나도 없으면 예외는 처리되지 않는다.

모든 예외 클래스는 Exception 클래스의 자손이므로, **catch블럭의 괄호()에 Exception 클래스 타입의 참조변수를 선언해 놓으면 어떤 종류의 예외가 발생하더라도 이 catch블럭에 의해서 처리된다.**

```java
class Ex8_3 {
	public static void main(String args[]) {
		System.out.println(1);			
		System.out.println(2);

		try {
			System.out.println(3);
			System.out.println(0/0);  // 0으로 나눠서 고의로 ArithmeticException을 발생시킨다. 
			System.out.println(4);  // 실행되지 않는다. 
		} catch (Exception e) {  // ArithmeticException 대신 Exception을 사용. 
			System.out.println(5);
		} // try-catch의 끝 

		System.out.println(6);
	} // main 메서드의 끝 
}
```

위 코드의 출력 결과는 다음과 같다.

```
1
2
3
5
6
```

**ArithmeticException 클래스는 Exception 클래스의 자손이므로 ArithmeticException 인스턴스와 Exception 클래스와의 instanceof 연산 결과가 true가 되어** Exception 클래스 타입의 참조변수를 선언한 catch 블럭의 문장들이 수행되고 예외가 처리되는 것이다.

```java
class Ex8_4 {
	public static void main(String args[]) {
		System.out.println(1);			
		System.out.println(2);
		try {
			System.out.println(3);
			System.out.println(0/0);
			System.out.println(4);  // 실행되지 않는다. 
		} catch (ArithmeticException ae) {
			if (ae instanceof ArithmeticException) 
				System.out.println("true");	
			System.out.println("ArithmeticException");
		} catch (Exception e) {
			System.out.println("Exception");
		} // try-catch의 끝 
		System.out.println(6);
	} // main 메서드의 끝 
}
```

위 코드의 출력 결과는 다음과 같다.

```
1
2
3
true
ArithmeticException
6
```

위의 예제에서는 두 개의 catch블럭, ArithmeticException 타입의 참조변수를 선언한 것과 Exception 타입의 참조변수를 선언한 것을 사용하였다.

try블럭에서 ArithmeticException이 발생하였으므로 instanceof 연산자로 catch블럭을 하나씩 차례대로 검사하게 되는데, 첫 번째 검사에서 일치하는 catch블럭을 찾았기 때문에 두 번째 catch블럭은 검사하지 않게 된다. 만일 try블럭 내에서 ArithmeticException이 아닌 다른 종류의 예외가 발생한 경우에는 두 번째 catch블럭인 Exception 타입의 참조변수를 선언한 곳에서 처리됐을 것이다.

이처럼, **try-catch문의 마지막에 Exception 타입의 참조변수를 선언한 catch블럭을 사용하면, 어떤 종류의 예외가 발생하더라도 이 catch블럭에 의해 처리되도록 할 수 있다.**
