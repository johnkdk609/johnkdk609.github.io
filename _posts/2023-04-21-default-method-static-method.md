---
layout: post
title: 디폴트 메서드와 static 메서드
description: Java의 정석 기초편 ch. 7 객체지향 프로그래밍 2
categories: Java
date: 2023-04-21 17:28:00 +09:00
---
원래는 인터페이스에 추상 메서드만 선언할 수 있는데, JDK1.8부터 디폴트 메서드와 static 메서드도 추가할 수 있게 되었다. static 메서드는 인스턴스와 관계가 없는 독립적인 메서드이기 때문에 예전부터 인터페이스에 추가하지 못할 이유가 없었다.

그러나 자바를 보다 쉽게 배울 수 있도록 규칙을 단순히 할 필요가 있어서 인터페이스의 모든 메서드는 추상 메서드이어야 한다는 규칙에 예외를 두지 않았다.

조상 클래스에 새로운 메서드를 추가하는 것은 별 일이 아니지만, 인터페이스의 경우에는 보통 큰 일이 아니다. 인터페이스에 메서드를 추가한다는 것은, 추상 메서드를 추가한다는 것이고, 이 인터페이스를 구현한 기존의 모든 클래스들이 새로 추가된 메서드를 구현해야 하기 때문이다. 

인터페이스가 변경되지 않으면 제일 좋겠지만, 아무리 설계를 잘해도 언젠가 변경은 발생하기 마련이다. JDK의 설계자들은 고심 끝에 **디폴트 메서드&#40;default method&#41;**라는 것을 고안해내었다.

디폴트 메서드는 추상 메서드의 기본적인 구현을 제공하는 메서드로, 추상 메서드가 아니기 때문에 디폴트 메서드가 새로 추가되어도 해당 인터페이스를 구현한 클래스를 변경하지 않아도 된다.

디폴트 메서드는 앞에 키워드 default를 붙이며, 추상 메서드와 달리 일반 메서드처럼 몸통{}이 있어야 한다. 디폴트 메서드 역시 접근 제어자가 public이며, 생략 가능하다.

```java
// 보기 1

interface MyInterface {
  void method();
  void newMethod();   // 추상 메서드
}
```

```java
// 보기 2

interface MyInterface {
  void method();
  default void newMethod() {}
}
```

보기 1 코드와 같이 newMethod()라는 추상 메서드를 추가하는 대신, 보기 2 코드와 같이 디폴트 메서드를 추가하면, 기존의 MyInterface를 구현한 클래스를 변경하지 않아도 된다. 즉, 조상 클래스에 새로운 메서드를 추가한 것과 동일해지는 것이다. 

대신, 새로 추가된 디폴트 메서드가 기존의 메서드와 이름이 중복되어 충돌하는 경우가 발생한다. 이 충돌을 해결하는 규칙은 다음과 같다.

> 1. **여러 인터페이스의 디폴트 메서드 간의 충돌**
> - 인터페이스를 구현한 클래스에서 디폴트 메서드를 오버라이딩해야 한다.
> 
> 2. **디폴트 메서드와 조상 클래스의 메서드 간의 충돌**
> - 조상 클래스의 메서드가 상속되고, 디폴트 메서드는 무시된다.

위의 규칙을 외우기 귀찮으면, 그냥 필요한 쪽의 메서드와 같은 내용으로 오버라이딩 해버리면 그만이다.


### 디폴트 메서드와 static 메서드 예제 코드

```java
class Ex7_11 {
	public static void main(String[] args) {
		Child3 c = new Child3();
		c.method1();
		c.method2();
		MyInterface.staticMethod(); 
		MyInterface2.staticMethod();
	}
}

class Child3 extends Parent3 implements MyInterface, MyInterface2 {
	public void method1() {	
		System.out.println("method1() in Child3");	// 오버라이딩 
	}			
}

class Parent3 {
	public void method2() {	
		System.out.println("method2() in Parent3");
	}
}

interface MyInterface {
	default void method1() { 
		System.out.println("method1() in MyInterface");
	}

	default void method2() { 
		System.out.println("method2() in MyInterface");
	}

	static void staticMethod() { 
		System.out.println("staticMethod() in MyInterface");
	}
}

interface MyInterface2 {
	default void method1() { 
		System.out.println("method1() in MyInterface2");
	}

	static void staticMethod() { 
		System.out.println("staticMethod() in MyInterface2");
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```java
method1() in Child3
method2() in Parent3
staticMethod() in MyInterface
staticMethod() in MyInterface2
```
