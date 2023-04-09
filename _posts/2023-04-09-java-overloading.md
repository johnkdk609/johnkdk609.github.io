---
layout: post
title: 오버로딩(overloading)
categories: Java
description: Java의 정석 기초편 ch. 6 객체지향 프로그래밍 1
date: 2023-04-09 09:02:00 +09:00
---
메서드도 변수와 마찬가지로 같은 클래스 내에서 서로 구별될 수 있어야 하기 때문에 각기 다른 이름을 가져야 한다. 그러나 자바에서는 한 클래스 내에 이미 사용하려는 이름과 같은 이름을 가진 메서드가 있더라도 매개변수의 개수 또는 타입이 다르면, 같은 이름을 사용해서 메서드를 정의할 수 있다.

이처럼, 한 클래스 내에 같은 이름의 메서드를 여러 개 정의하는 것을 '메서드 오버로딩(method overloading)' 또는 간단히 '오버로딩(overloading)'이라 한다.

같은 이름의 메서드를 정의한다고 해서 무조건 오버로딩인 것은 아니다. 오버로딩이 성립하기 위해서는 다음과 같은 조건을 만족해야 한다.

> 1. 메서드 이름이 같아야 한다.
> 2. 매개변수의 개수 또는 타입이 달라야 한다.
> 3. 반환 타입은 관계 없다.

비록 메서드의 이름이 같다 하더라도 매개변수가 다르면 서로 구별될 수 있기 때문에 오버로딩이 가능한 것이다. 위의 조건을 만족시키지 못하는 메서드는 중복 정의로 간주되어 컴파일 시에 에러가 발생한다. 그리고 오버로딩된 메서드들은 매개변수에 의해서만 구별될 수 있으므로 반환 타입은 오버로딩을 구현하는 데에 아무런 영향을 주지 못한다는 것에 주의해야 한다. 

오버로딩의 예로 가장 대표적인 것은 println 메서드이다. println 메서드를 호출할 때 매개변수로 지정하는 값의 타입에 따라서 호출되는 println 메서드가 달라진다.

PrintStream 클래스에는 어떤 종류의 매개변수를 지정해도 출력할 수 있도록 아래와 같이 10개의 오버로딩된 println 메서드를 정의해 놓고 있다.

- void println()
- void println(boolean x)
- void println(char x)
- void println(char[] x)
- void println(double x)
- void println(float x)
- void println(int x)
- void println(long x)
- void println(Object x)
- void println(String x)

println 메서드를 호출할 때 매개변수로 넘겨주는 값의 타입에 따라서 위의 오버로딩된 메서드들 중의 하나가 선택되어 실행되는 것이다.


몇 가지 예를 들어 오버로딩에 대해 자세히 설명하고자 한다.

```java
// 보기 1

int add(int a, int b) { return a + b; }
int add(int x, int y) { return x + y; }
```

위의 두 메서드는 매개변수의 이름만 다를 뿐 매개변수의 타입이 같기 때문에 오버로딩이 성립하지 않는다. 매개변수의 이름이 다르면 메서드 내에서 사용되는 변수의 이름이 달라질 뿐, 아무런 의미가 없다. 그래서 이 두 메서드는 정확히 같은 것이다. 마치 수학에서 'f(x) = x + 1'과 'f(a) = a + 1'이 같은 표현인 것과 같다.

컴파일하면, 'add(int, int) is already defined(이미 같은 메서드가 정의되었다).'라는 메세지가 나타날 것이다.


```java
// 보기 2

int add(int a, int b) { return a + b; }
long add(int a, int b) { return (long)(a + b); }
```

이번 경우는 리턴 타입만 다른 경우이다. 매개변수의 타입과 개수가 일치하기 때문에 add(3, 3)과 같이 호출하였을 때 어떤 메서드가 호출된 것인지 결정할 수 없기 때문에 오버로딩으로 간주되지 않는다.

이 경우 역시 컴파일하면, 'add(int, int) is already defined(이미 같은 메서드가 정의되었다).'라는 메세지가 나타날 것이다.


```java
// 보기 3

long add(int a, long b) { return a + b; }
long add(long a, int b) { return a + b; }
```

두 메서드 모두 int형과 long형 매개변수가 하나씩 선언되어 있지만, 서로 순서가 다른 경우이다. 이 경우에는 호출 시 매개변수의 값에 의해 호출될 메서드가 구분될 수 있으므로 중복된 메서드 정의가 아닌, 오버로딩으로 간주한다.

이처럼 단지 매개변수의 순서만을 다르게 하여 오버로딩을 구현하면, 사용자가 매개변수의 순서를 외우지 않아도 되는 장점이 있지만, 오히려 단점이 될 수도 있기 때문에 주의해야 한다.

예를 들어 add(3, 3L)과 같이 호출되면 첫 번째 메서드가, add(3L, 3)과 같이 호출되면 두 번째 메서드가 호출된다. 단, 이 경우에는 add(3, 3)과 같이 호출할 수 없다. 이와 같이 호출할 경우, 두 메서드 중 어느 메서드가 호출될 것인지 알 수 없기 때문에 메서드를 호출하는 곳에서 컴파일 에러가 발생한다. 


### 오버로딩(overloading) 예제 코드

```java
class Ex6_10 {
	public static void main(String args[]) {
		MyMath3 mm = new MyMath3();
		System.out.println("mm.add(3, 3) 결과:"    + mm.add(3,3));
		System.out.println("mm.add(3L, 3) 결과: "  + mm.add(3L,3));
		System.out.println("mm.add(3, 3L) 결과: "  + mm.add(3,3L));
		System.out.println("mm.add(3L, 3L) 결과: " + mm.add(3L,3L));

		int[] a = {100, 200, 300};
		System.out.println("mm.add(a) 결과: " + mm.add(a));
   }
}

class MyMath3 {
	int add(int a, int b) {
		System.out.print("int add(int a, int b) - ");
		return a+b;
	}
	
	long add(int a, long b) {
		System.out.print("long add(int a, long b) - ");
		return a+b;
	}
	
	long add(long a, int b) {
		System.out.print("long add(long a, int b) - ");
		return a+b;
	}

	long add(long a, long b) {
		System.out.print("long add(long a, long b) - ");
		return a+b;
	}

	int add(int[] a) {		// 배열의 모든 요소의 합을 결과로 돌려준다. 
		System.out.print("int add(int[] a) - ");
		int result = 0;
		for(int i=0; i < a.length;i++) 
			result += a[i];
		
		return result;
	}
}
```

이 코드의 출력 결과는 다음과 같다.

```
int add(int a, int b) - mm.add(3, 3) 결과:6
long add(long a, int b) - mm.add(3L, 3) 결과: 6
long add(int a, long b) - mm.add(3, 3L) 결과: 6
long add(long a, long b) - mm.add(3L, 3L) 결과: 6
int add(int[] a) - mm.add(a) 결과: 600
```

실행 결과를 보면 어떻게 add 메서드가 println 메서드보다 먼저 호출되는지 의아할 수 있다.

```java
System.out.println("mm.add(3, 3) 결과:" + mm.add(3, 3));
```

간단히 위의 문장이 아래의 두 문장을 하나로 합친 것이라고 생각하면 이해가 쉽다.

```java
int result = mm.add(3, 3);
System.out.println("mm.add(3, 3) 결과:" + result);
```
