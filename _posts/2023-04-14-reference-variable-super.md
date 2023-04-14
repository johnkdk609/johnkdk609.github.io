---
layout: post
title: 참조변수 super
description: Java의 정석 기초편 ch. 7 객체지향 프로그래밍 2
categories: Java
date: 2023-04-14 15:58:00 +09:00
---
super는 자손 클래스에서 조상 클래스로부터 상속받은 멤버를 참조하는 데에 사용되는 참조변수이다. 멤버변수와 지역변수의 이름이 같을 때 this를 붙여서 구별했듯이 상속받은 멤버와 자신의 멤버가 이름이 같을 때는 super를 붙여서 구별할 수 있다. 

```java
class Ex7_2 {
	public static void main(String args[]) {
		Child c = new Child();
		c.method();
	}
}

class Parent { int x = 10; /* super.x */ }

class Child extends Parent {
	int x = 20;		// this.x

	void method() {
		System.out.println("x=" + x);
		System.out.println("this.x=" + this.x);
		System.out.println("super.x=" + super.x);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
x=20
this.x=20
super.x=10
```

위의 예제에서 Child 클래스는 조상인 Parent 클래스로부터 x를 상속받는데, 공교롭게도 자신의 멤버인 x와 이름이 같아서 이 둘을 구분할 방법이 필요하다. 바로 이럴 때 사용하는 것이 super이다.

아래의 경우 x, this.x, super.x 모두 같은 변수를 의미하므로 모두 같은 값이 출력되었다.

```java
class Ex7_3 {
	public static void main(String args[]) {
		Child2 c = new Child2();
		c.method();
	}
}

class Parent2 { int x = 10; /* super.x와 this.x 둘 다 가능 */ }

class Child2 extends Parent2 {
	void method() {
		System.out.println("x=" + x);
		System.out.println("this.x=" + this.x);
		System.out.println("super.x=" + super.x);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
x=10
this.x=10
super.x=10
```

모든 인스턴스 메서드에는 this와 super가 지역변수로 존재하는데, 이들에는 자신이 속한 인스턴스의 주소가 자동으로 저장된다. 조상의 멤버와 자신의 멤버를 구별하는 데에 사용된다는 점만 제외하면 this와 super은 근본적으로 같다.
