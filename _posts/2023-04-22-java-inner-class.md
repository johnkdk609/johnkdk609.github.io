---
layout: post
title: 내부 클래스(inner class)
description: Java의 정석 기초편 ch. 7 객체지향 프로그래밍 2
categories: Java
date: 2023-04-22 11:40:00 +09:00
---
내부 클래스(inner class)는 클래스 내에 선언된 클래스이다. 클래스에 다른 클래스를 선언하는 이유는 간단하다. 두 클래스가 서로 긴밀한 관계에 있기 때문이다.

한 클래스를 다른 클래스의 내부 클래스로 선언하면 두 클래스의 멤버들 간에 서로 쉽게 접근할 수 있다는 장점과, 외부에는 불필요한 클래스를 감춤으로써 코드의 복잡성을 줄일 수 있다는 장점을 얻을 수 있다.

> **내부 클래스의 장점**
> - **내부 클래스에서 외부 클래스의 멤버들에 쉽게 접근할 수 있다.**
> - **코드의 복잡성을 줄일 수 있다.** (캡슐화)

아래 보기 1 코드의 A와 B 두 개의 독립적인 클래스를 보기 2 코드처럼 바꾸면 B는 A의 내부 클래스(inner class)가 되고 A는 B를 감싸고 있는 외부 클래스(outer class)가 된다.

```java
// 보기 1

class A {
  // ...
}

class B {
  // ...
}
```

```java
// 보기 2

class A {     // 외부 클래스
  // ...
  class B {   // 내부 클래스
    // ...
  }
  // ...
}
```

이때 내부 클래스인 B는 외부 클래스인 A를 제외하고는 다른 클래스에서 잘 사용되지 않는 것이어야 한다.

내부 클래스는 클래스 내에 선언된다는 점을 제외하고는 일반적인 클래스와 다르지 않다. 다만 앞으로 배우게 될 내부 클래스의 몇 가지 특징만 잘 이해하면 실제로 활용하는 데에 어려움이 없을 것이다.


### 내부 클래스의 종류와 특징

내부 클래스의 종류는 변수의 선언 위치에 따른 종류와 같다. 내부 클래스는 마치 변수를 선언하는 것과 같은 위치에 선언할 수 있으며, 변수의 선언 위치에 따라 인스턴스 변수, 클래스 변수(static 변수), 지역변수로 구분되는 것과 같이 내부 클래스도 선언 위치에 따라 다음과 같이 구분되어진다. 내부 클래스의 유효범위와 성질이 유사하므로 서로 비교해보면 이해하는 데에 많은 도움이 된다. 

<table>
  <tr>
    <th>내부 클래스</th>
    <th>특징</th>
  </tr>
  <tr>
    <td>인스턴스 클래스<br>(instance class)</td>
    <td>외부 클래스의 멤버변수로 선언 위치에 선언하며, 외부 클래스의 인스턴스 멤버처럼 다뤄진다. 주로 외부 클래스의 인스턴스 멤버들과 관련된 작업에 사용될 목적으로 선언된다.</td>
  </tr>
  <tr>
    <td>스태틱 클래스<br>(static class)</td>
    <td>외부 클래스의 멤버변수 선언 위치에 선언하며, 외부 클래스의 static 멤버처럼 다뤄진다. 주로 외부 클래스의 static 멤버, 특히 static 메서드에서 사용될 목적으로 선언된다.</td>
  </tr>
  <tr>
    <td>지역 클래스<br>(local class)</td>
    <td>외부 클래스의 메서드나 초기화 블럭 안에 선언하며, 선언된 영역 내부에서만 사용될 수 있다.</td>
  </tr>
  <tr>
    <td>익명 클래스<br>(anonymous class)</td>
    <td>클래스의 선언과 객체의 생성을 동시에 하는 이름 없는 클래스(일회용)</td>
  </tr>
</table>


### 내부 클래스의 선언

아래의 보기 4 코드에는 외부 클래스(Outer)에 3개의 서로 다른 종류의 내부 클래스가 선언되어 있다. 양쪽의 코드(보기 3, 보기 4)를 비교해보면 내부 클래스의 선언 위치가 변수의 선언 위치와 동일함을 알 수 있다.

변수가 선언된 위치에 따라 인스턴스 변수, 클래스 변수(static 변수), 지역변수로 나뉘듯이 내부 클래스도 이와 마찬가지로 선언된 위치에 따라 나뉜다. 그리고, 각 내부 클래스의 선언 위치에 따라 같은 선언 위치의 변수와 동일한 유효범위(scope)와 접근성(accessibility)을 갖는다. 

```java
// 보기 3

class Outer {
  int iv = 0;
  static int cv = 0;
  
  void myMethod() {
    int lv = 0;
  }
}
```

```java
// 보기 4

class Outer {
  class InstanceInner {}
  static class StaticInner {}
  
  void myMethod() {
    class LocalInner {}
  }
}
```


### 내부 클래스의 제어자와 접근성

아래 코드(보기 6)에서 인스턴스 클래스(InstanceInner)와 스태틱 클래스(StaticInner)은 외부 클래스(Outer)의 멤버변수(인스턴스 변수와 클래스 변수)와 같은 위치에 선언되며, 또한 멤버변수와 같은 성질을 갖는다. 따라서 내부 클래스와 외부 클래스의 멤버가 같이 간주되고, 인스턴스 멤버와 static 멤버 간의 규칙이 내부 클래스에도 똑같이 적용된다. 

```java
// 보기 5

class Outer {
  private int iv = 0;
  protected static int cv = 0;
  
  void myMethod() {
    int lv = 0;
  }
}
```

```java
// 보기 6

class Outer {
  private class InstanceInner {}
  protected static class StaticInner {}
  
  void myMethod() {
    class LocalInner {}
  }
}
```

그리고 내부 클래스도 클래스이기 때문에 abstract나 final과 같은 제어자를 사용할 수 있을 뿐만 아니라, 멤버변수들처럼 private, protected과 접근 제어자도 사용이 가능하다.


### 내부 클래스의 제어자와 접근성 예제 1

```java
class Ex7_12 { 
	class InstanceInner { 
		int iv = 100; 
//		static int cv = 100;            // ERROR! static 변수를 선언할 수 없다.  
		final static int CONST = 100;   // final static은 상수이므로 허용 
	} 

   	static class StaticInner { 
		int iv = 200; 
		static int cv = 200;    // static 클래스만 static 멤버를 정의할 수 있다. 
	} 

	void myMethod() { 
		class LocalInner { 
			int iv = 300; 
//			static int cv = 300;            // ERROR! static 변수를 선언할 수 없다.  
			final static int CONST = 300;   // final static은 상수이므로 허용 
		} 
	} 

	public static void main(String args[]) { 
		System.out.println(InstanceInner.CONST); 
		System.out.println(StaticInner.cv); 
	} 
}
```

위 코드의 출력 결과는 다음과 같다.

```
100
200
```

내부 클래스 중에서 스태틱 클래스(StaticInner)만 static 멤버를 가질 수 있다. 드문 경우지만 내부 클래스에 static 변수를 선언해야 한다면 스태틱 클래스로 선언해야 한다.

다만 final과 static이 동시에 붙은 변수는 상수(constant)이므로 모든 내부 클래스에서 정의가 가능하다.


### 내부 클래스의 제어자와 접근성 예제 2

```java
class Ex7_13 {
	class InstanceInner {}
	static class StaticInner {}

	// 인스턴스 멤버 간에는 서로 직접 접근이 가능하다. 
	InstanceInner iv = new InstanceInner();
	// static 멤버 간에는 서로 직접 접근이 가능하다. 
	static StaticInner cv = new StaticInner();

	static void staticMethod() {
                // static 멤버는 인스턴스 멤버에 직접 접근할 수 없다. 
//	        InstanceInner obj1 = new InstanceInner();	
		StaticInner obj2 = new StaticInner();

                // 굳이 접근하려면 아래와 같이 객체를 생성해야 한다. 
                // 인스턴스 클래스는 외부 클래스를 먼저 생성해야만 생성할 수 있다. 
		Ex7_13 outer = new Ex7_13();
		InstanceInner obj1 = outer.new InstanceInner();
	}

	void instanceMethod() {
                // 인스턴스 메서드에서는 인스턴스 멤버와 static 멤버 모두 접근 가능하다. 
		InstanceInner obj1 = new InstanceInner();
		StaticInner obj2 = new StaticInner();
	        // 메서드 내에 지역적으로 선언된 내부 클래스는 외부에서 접근할 수 없다. 
//              LocalInner lv = new LocalInner();
	}

	void myMethod() {
		class LocalInner {}
		LocalInner lv = new LocalInner();
	}
}
```

인스턴스 멤버는 같은 클래스에 있는 인스턴스 멤버와 static 멤버 모두 직접 호출이 가능하지만, static 멤버는 인스턴스 멤버를 직접 호출할 수 없는 것처럼, **인스턴스 클래스는 외부 클래스의 인스턴스 멤버를 객체 생성 없이 바로 사용할 수 있지만, 스태틱 클래스는 외부 클래스의 인스턴스 멤버를 객체 생성 없이 사용할 수 없다.**

마찬가지로 인스턴스 클래스는 스태틱 클래스의 멤버들을 객체 생성 없이 사용할 수 있지만, 스태틱 클래스에서는 인스턴스 클래스의 멤버들을 객체 생성 없이 사용할 수 없다.


### 내부 클래스의 제어자와 접근성 예제 3

```java
class Outer {
	private int outerIv = 0;
	static  int outerCv = 0;

	class InstanceInner {
		int iiv  = outerIv;  // 외부 클래스의 private 멤버도 접근 가능하다. 
		int iiv2 = outerCv;
	}

	static class StaticInner {
//      스태틱 클래스는 외부 클래스의 인스턴스 멤버에 접근할 수 없다. 
//		int siv = outerIv;
		static int scv = outerCv;
	}

	void myMethod() {
		int lv = 0;
		final int LV = 0;  // JDK1.8부터 final 생략 가능 

		class LocalInner {
			int liv  = outerIv;
			int liv2 = outerCv;
//			외부 클래스의 지역변수는 final이 붙은 변수(상수)만 접근 가능하다. 
//			int liv3 = lv;	// ERROR!!! (JDK1.8부터 에러 아님) 
			int liv4 = LV;	// OK
		}
	}
}
```

내부 클래스에서 외부 클래스의 변수들에 대한 접근성을 보여주는 예제이다. 인스턴스 클래스(InstanceInner)는 외부 클래스(Outer)의 인스턴스 멤버이기 때문에 인스턴스 변수 outerIv와 static 변수 outerCv를 모두 사용할 수 있다. 심지어는 outerIv의 접근 제어자가 private 일지라도 사용 가능하다. 

스태틱 클래스(StaticInner)는 외부 클래스(Outer)의 static 멤버이기 때문에 외부 클래스의 인스턴스 멤버인 outerIv와 InstanceInner를 사용할 수 없다. 단지 static 멤버인 outerCv만을 사용할 수 있다. 

지역 클래스(LocalInner)는 외부 클래스의 인스턴스 멤버와 static 멤버를 모두 사용할 수 있으며, 지역 클래스가 포함된 메서드에 정의된 지역변수도 사용할 수 있다. 단, final이 붙은 지역변수만 접근 가능한데 그 이유는 메서드가 수행을 마쳐서 지역변수가 소멸된 시점에도, 지역 클래스의 인스턴스가 소멸된 지역변수를 참조하려는 경우가 발생할 수 있기 때문이다. 

JDK1.8부터 지역 클래스에서 접근하는 지역변수 앞에 final을 생략할 수 있게 바뀌었다. 대신 컴파일러가 자동으로 붙여준다. 즉, 편의상 final을 생략할 수 있게 한 것일 뿐 해당 변수의 값이 바뀌는 문장이 있으면 컴파일 에러가 발생한다. 


### 내부 클래스의 제어자와 접근성 예제 4

```java
class Outer2 {
	class InstanceInner {
		int iv = 100;
	}

	static class StaticInner {
		int iv = 200;
		static int cv = 300;
	}

	void myMethod() {
		class LocalInner {
			int iv = 400;
		}
	}
}

class Ex7_15 {
	public static void main(String[] args) {
		// 인스턴스 클래스의 인스턴스를 생성하려면 
		// 외부 클래스의 인스턴스를 먼저 생성해야 한다. 
		Outer2 oc = new Outer2();
		Outer2.InstanceInner ii = oc.new InstanceInner();

		System.out.println("ii.iv : "+ ii.iv);
		System.out.println("Outer2.StaticInner.cv : "+Outer2.StaticInner.cv);
                                     
                // 스태틱 내부 클래스의 인스턴스는 외부 클래스를 먼저 생성하지 않아도 된다. 
		Outer2.StaticInner si = new Outer2.StaticInner();
		System.out.println("si.iv : "+ si.iv);
	}
}
```

위 코드의 출력 결과는 다음과 같다. 

```
ii.iv : 100
Outer2.StaticInner.cv : 300
si.iv : 200
```

외부 클래스가 아닌 다른 클래스에서 내부 클래스를 생성하고 내부 클래스의 멤버에 접근하는 예제이다. 실제로 이런 경우가 발생했다는 것은 내부 클래스로 선언해서는 안 되는 클래스를 내부 클래스로 선언했다는 의미이다. 참고로만 봐두고 가볍게 넘어가도록 하자.

참고로 컴파일 시 생성되는 클래스 파일은 다음과 같다.

**Ex7_15.class**\
**Outer2.class**\
**Outer2$InstanceInner.class**\
**Outer2$StaticInner.class**\
**Outer2$1LocalInner.class**


### 내부 클래스의 제어자와 접근성 예제 5

```java
class Outer3 {
	int value = 10;   // Outer3.this.value

	class Inner {
		int value = 20;   // this.value

		void method1() {
			int value = 30;
			System.out.println("            value :" + value);
			System.out.println("       this.value :" + this.value);
			System.out.println("Outer3.this.value :" + Outer3.this.value);
		}
	}   // Inner 클래스의 끝 
}   // Outer3 클래스의 끝 

class Ex7_16 {
	public static void main(String args[]) {
		Outer3 outer = new Outer3();
		Outer3.Inner inner = outer.new Inner();
		inner.method1();
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
            value :30
       this.value :20
Outer3.this.value :10
```

위의 예제는 내부 클래스와 외부 클래스에 선언된 변수의 이름이 같을 때 변수 앞에 'this' 또는 '**외부 클래스명.this**'를 붙여서 서로 구별할 수 있다는 것을 보여준다.
