---
layout: post
title: 함수형 인터페이스(Functional Interface)
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-13 14:04:00 +0900
---
예를 들어 아래와 같이 메서드 max가 선언된 MyFunction인터페이스가 정의되어 있다고 가정하자.

```java
interface MyFunction {
    public abstract int max(int a, int b);
}
```

그러면 이 인터페이스를 구현한 익명 클래스의 객체는 다음과 같이 생성할 수 있다.

```java
MyFunction f = new MyFunction() {
                    public int max(int a, int b) {
                        return a > b ? a : b;
                    }
                };

int big = f.max(5, 3);      // 익명 객체의 메서드를 호출
```

MyFunction인터페이스에 정의된 메서드 max()는 람다식 '(int a, int b) -> a > b ? a : b'과 메서드의 선언부가 일치한다. 그래서 위 코드의 익명 객체를 람다식으로 아래와 같이 대체할 수 있다.

```java
MyFunction f = (int a, int b) -> a > b ? a : b;     // 익명 객체를 람다식으로 대체
int big = f.max(5, 3);      // 익명 객체의 메서드를 호출
```

이처럼 MyFunction인터페이스를 구현한 익명 객체를 람다식으로 대체가 가능한 이유는, 람다식도 실제로는 익명 객체이고, MyFunction인터페이스를 구현한 익명 객체의 메서드 max()와 람다식의 매개변수의 타입과 개수 그리고 반환값이 일치하기 때문이다.

지금까지 살펴본 것처럼, 하나의 메서드가 선언된 인터페이스를 정의해서 람다식을 다루는 것은 기존의 자바의 규칙들을 어기지 않으면서도 자연스럽다.

그래서 인터페이스를 통해 람다식을 다루기로 결정되었으며, 람다식을 다루기 위한 인터페이스를 '함수형 인터페이스(functional interface)'라고 부르기로 했다.

```java
@FunctionalInterface
interface MyFunction {      // 함수형 인터페이스 MyFunction을 정의
    public abstract int max(int a, int b);
}
```

단, 함수형 인터페이스에는 오직 하나의 추상 메서드만 정의되어 있어야 한다는 제약이 있다. 그래야 람다식과 인터페이스의 메서드가 1:1로 연결될 수 있기 때문이다.


## 함수형 인터페이스 타입의 매개변수, 반환 타입

함수형 인터페이스 MyFunction이 아래와 같이 정의되어 있을 때,

```java
@FunctionalInterface
interface MyFunction {
    void myMethod();        // 추상 메서드
}
```

메서드의 매개변수가 MyFunction타입이면, 이 메서드를 호출할 때 람다식을 참조하는 참조변수를 매개변수로 지정해야 한다는 뜻이다.

```java
void aMethod(MyFunction f) {    // 매개변수의 타입이 함수형 인터페이스
    f.myMethod();               // MyFunction에 정의된 메서드 호출
}
        ...
MyFunction f = () -> System.out.println("myMethod()");
aMethod(f);
```

또는 참조변수 없이 아래와 같이 직접 람다식을 매개변수로 지정하는 것도 가능하다.

```java
aMethod(() -> System.out.println("myMethod()"));    // 람다식을 매개변수로 지정
```

그리고 메서드의 반환타입이 함수형 인터페이스 타입이라면, 이 함수형 인터페이스의 추상메서드와 동등한 람다식을 가리키는 참조변수를 반환하거나 람다식을 직접 반환할 수 있다.

```java
MyFunction myMethod() {
    MyFunction f = () -> {};
    return f;       // 이 줄과 윗 줄을 한 줄로 줄이면, return () -> {};
}
```

람다식을 참조변수로 다룰 수 있다는 것은 메서드를 통해 람다식을 주고받을 수 있다는 것을 의미한다. 즉, 변수처럼 메서드를 주고받는 것이 가능해진 것이다.

사실상 메서드가 아니라 객체를 주고받는 것이라 근본적으로 달라진 것은 아무것도 없지만, 람다식 덕분에 예전보다 코드가 더 간결하고 이해하기 쉬워졌다.


## 예제

```java
@FunctionalInterface
interface MyFunction {
	void run();  // public abstract void run();
}

class Ex14_1 {
	static void execute(MyFunction f) { // 매개변수의 타입이 MyFunction인 메서드 
		f.run();
	}

	static MyFunction getMyFunction() { // 반환 타입이 MyFunction인 메서드 
		MyFunction f = () -> System.out.println("f3.run()");
		return f;
	}

	public static void main(String[] args) {
		// 람다식으로 MyFunction의 run()을 구현 
		MyFunction f1 = () -> System.out.println("f1.run()");

		MyFunction f2 = new MyFunction() {  // 익명클래스로 run()을 구현 
			public void run() {   // public을 반드시 붙여야 함 
				System.out.println("f2.run()");
			}
		};

		MyFunction f3 = getMyFunction();

		f1.run();
		f2.run();
		f3.run();

		execute(f1);
		execute( () -> System.out.println("run()") );
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
f1.run()
f2.run()
f3.run()
f1.run()
run()
```