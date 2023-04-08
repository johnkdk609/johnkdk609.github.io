---
layout: post
title: static 메서드와 인스턴스 메서드
categories: Java
date: 2023-04-08 17:37:00 +09:00
description: Java의 정석 기초편 ch. 6 객체지향 프로그래밍 1
---
변수에서 그랬던 것과 같이, 메서드 앞에 static이 붙어 있으면 클래스 메서드이고, 붙어 있지 않으면 인스턴스 메서드이다.

클래스 메서드도 클래스 변수처럼, 객체를 생성하지 않고도 '클래스이름.메서드이름(매개변수)'와 같은 식으로 호출이 가능하다. 반면에 **인스턴스 메서드는 반드시 객체를 생성해야만 호출할 수 있다**.

그렇다면 클래스를 정의할 때, 어느 경우에 static을 사용해서 클래스 메서드로 정의해야하는 것일까?

클래스는 '**데이터(변수)와 데이터에 관련된 메서드의 집합**'이므로, 같은 클래스 내에 있는 메서드와 멤버변수는 아주 밀접한 관계가 있다. (멤버변수란 클래스 영역에 선언된 변수로, 인스턴스 변수 & static 변수가 해당된다.)

인스턴스 메서드는 인스턴스 변수와 관련된 작업을 하는, 즉 메서드의 작업을 수행하는데 인스턴스 변수를 필요로 하는 메서드이다. 그런데 인스턴스 변수는 인스턴스(객체)를 생성해야만 만들어지므로 **인스턴스 메서드 역시 인스턴스를 생성해야만 호출할 수 있는 것**이다. 

반면에 메서드 중에서 인스턴스와 관계없는(인스턴스 변수나 인스턴스 메서드를 사용하지 않는) 메서드를 클래스 메서드(static 메서드)로 정의한다.

물론 인스턴스 변수를 사용하지 않는다고 해서 반드시 클래스 메서드로 정의해야하는 것은 아니지만 특별한 이유가 없는 한 그렇게 하는 것이 일반적이다.


### static 메서드와 인스턴스 메서드 예제 코드

```java
class MyMath2 {
	long a, b;

	// 인스턴스 변수 a, b만을 이용해서 작업하므로 매개변수가 필요 없다. 
	long add() { return a + b; }  // a, b는 인스턴스 변수 
	long subtract() { return a - b; }
	long multiply() { return a * b; }
	double divide() { return a / b; }

	// 인스턴스 변수와 관계없이 매개변수만으로 작업이 가능하다. 
	static long add(long a, long b) { return a + b; }  // a, b는 지역변수 
	static long subtract(long a, long b) { return a - b; }
	static long multiply(long a, long b) { return a * b; }
	static double divide(long a, long b) { return a / (double)b; }
}

class Ex6_9 {
	public static void main(String args[]) {
		// 클래스 메서드 호출. 인스턴스 생성 없이 호출 가능 
		System.out.println(MyMath2.add(200L, 100L));
		System.out.println(MyMath2.subtract(200L, 100L));
		System.out.println(MyMath2.multiply(200L, 100L));
		System.out.println(MyMath2.divide(200L, 100L));

		MyMath2 mm = new MyMath2(); // 인스턴스를 생성 
		mm.a = 200L;
		mm.b = 100L;
		// 인스턴스 메서드는 객체 생성 후에만 호출이 가능하다. 
		System.out.println(mm.add());
		System.out.println(mm.subtract());
		System.out.println(mm.multiply());
		System.out.println(mm.divide());
   }
}
```

위 코드의 출력 결과는 다음과 같다.

```
300
100
20000
2.0
300
100
20000
2.0
```

인스턴스 메서드인 add(), subtract(), multiply(), divide()는 인스턴스 변수인 a와 b만으로도 충분히 작업이 가능하기 때문에, 매개변수를 필요로 하지 않으므로 괄호()에 매개변수를 선언하지 않았다.

반면에 add(long a, long b), subtract(long a, long b) 등은 인스턴스 변수 없이 매개변수만으로 작업을 수행하기 때문에 static을 붙여서 클래스 메서드로 선언하였다.

그래서 위 코드의 main 메서드를 보면, 클래스 메서드는 객체 생성 없이 바로 호출이 가능했고, 인스턴스 메서드는 MyMath2 클래스의 인스턴스를 생성한 후에야 호출이 가능했다.


### static을 언제 붙여야 할까?

#### 1. 클래스를 설계할 때, 멤버변수 중 모든 인스턴스에 공통으로 사용하는 것에 static을 붙인다.
- 생성된 각 인스턴스는 서로 독립적이기 때문에 각 인스턴스의 변수(iv)는 서로 다른 값을 유지한다. 그러나 모든 인스턴스에서 같은 값이 유지되어야 하는 변수는 static을 붙여서 클래스 변수로 정의해야 한다.

#### 2. 클래스 변수(static 변수)는 인스턴스를 생성하지 않아도 사용할 수 있다.
- static이 붙은 변수(클래스 변수)는 클래스가 메모리에 올라갈 때 이미 자동적으로 생성되기 때문이다.

#### 3. 클래스 메서드(static 메서드)는 인스턴스 변수를 사용할 수 없다.
- 인스턴스 변수는 인스턴스가 반드시 존재해야만 사용할 수 있는데, 클래스 메서드(static이 붙은 메서드)는 인스턴스 생성 없이 호출 가능하므로 클래스 메서드가 호출되었을 때 인스턴스가 존재하지 않을 수도 있다. 그래서 클래스 메서드에서 인스턴스 변수의 사용을 금지한다. 반면에 인스턴스 변수나 인스턴스 메서드에서는 static이 붙은 멤버들을 사용하는 것이 언제나 가능하다. 인스턴스 변수가 존재한다는 것은 static 변수가 이미 메모리에 존재한다는 것을 의미하기 때문이다.

#### 4. 메서드 내에서 인스턴스 변수를 사용하지 않는다면, static을 붙이는 것을 고려한다.
- 메서드의 작업 내용 중에서 인스턴스 변수를 필요로 한다면, static을 붙일 수 없다. 반대로 인스턴스 변수를 필요로 하지 않는다면 static을 붙이자. 메서드 호출시간이 짧아지므로 성능이 향상된다. static을 안 붙인 메서드(인스턴스 메서드)는 실행 시 호출되어야할 메서드를 찾는 과정이 추가적으로 필요하기 때문에 시간이 더 걸린다.


> - 클래스의 멈버변수 중 모든 인스턴스에 공통된 값을 유지해야 하는 것이 있는지 살펴보고 있으면, static을 붙여준다.
> - 작성한 메서드 중에서 인스턴스 변수나 인스턴스 메서드를 사용하지 않는 메서드에 static을 붙일 것을 고려한다.


### 메서드 간의 호출과 참조

같은 클래스에 속한 멤버들 간에는 별도의 인스턴스를 생성하지 않고도 서로 참조 또는 호출이 가능하다. 단, 클래스 멤버가 인스턴스 멤버를 참조 또는 호출하고자 하는 경우에는 인스턴스를 생성해야 한다.

그 이유는 **인스턴스 멤버가 존재하는 시점에 클래스 멤버는 항상 존재하지만, 클래스 멤버가 존재하는 시점에 인스턴스 멤버가 존재하지 않을 수도 있기 때문**이다.

(인스턴스 멤버란 인스턴스 변수와 인스턴스 메서드를 의미한다.)


```java
class TestClass {
  void instanceMethod() {}    // 인스턴스 메서드
  static void staticMethod() {}   // static 메서드
  
  void instanceMethod() {   // 인스턴스 메서드
    instanceMethod();   // 다른 인스턴스 메서드를 호출한다.
    staticMethod();   // static 메서드를 호출한다.
  }
  
  static void staticMethod2() {   // static 메서드
    instanceMethod();   // ERROR!! 인스턴스 메서드를 호출할 수 없다.
    staticMethod();   // static 메서드는 호출할 수 있다.
  }
} // end of class 
```

위의 코드는 같은 클래스 내의 인스턴스 메서드와 static 메서드 간의 호출에 대해서 설명하고 있다. 같은 클래스 내의 메서드는 서로 객체의 생성이나 참조변수 없이 직접 호출이 가능하지만 static 메서드는 인스턴스 메서드를 호출할 수 없다.


```java
class TestClass2 {
  int iv;         // 인스턴스 변수
  static int cv;  // 클래스 변수
  
  void instanceMethod() {   // 인스턴스 메서드
    System.out.println(iv); // 인스턴스 변수를 사용할 수 있다.
    System.out.println(cv); // 클래스 변수를 사용할 수 있다.
  }
  
  static void staticMethod() {  // static 메서드
    System.out.println(iv);  // ERROR!! 인스턴스 변수를 사용할 수 없다.
    System.out.println(cv);  // 클래스 변수를 사용할 수 있다.
  }
} // end of class
```

이번에는 변수와 메서드 간의 호출에 대해서 살펴보자. 메서드 간의 호출에서처럼 인스턴스 메서드는 인스턴스 변수를 사용할 수 있지만, static 메서드는 인스턴스 변수를 사용할 수 없다.
