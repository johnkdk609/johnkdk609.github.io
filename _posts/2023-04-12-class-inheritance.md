---
layout: post
title: 클래스 간의 관계 - 상속(inheritance)
description: Java의 정석 기초편 ch. 7 객체지향 프로그래밍 2
categories: Java
date: 2023-04-12 22:17:00 +09:00
---
상속(inheritance)이란, 기존의 클래스를 재사용하여 새로운 클래스를 작성하는 것이다. 상속을 통해서 클래스를 작성하면 보다 적은 양의 코드로 새로운 클래스를 작성할 수 있고 코드를 공통적으로 관리할 수 있기 때문에 코드의 추가 및 변경이 매우 용이하다.

이러한 특징은 코드의 재사용성을 높이고 코드의 중복을 제거하여 프로그램의 생산성과 유지보수에 크게 기여한다.

자바에서 상속을 구현하는 방법은 아주 간단하다. 새로 작성하고자 하는 클래스의 이름 뒤에 상속받고자 하는 클래스의 이름을 키워드 'extends'와 함께 써주기만 하면 된다.

예를 들어 새로 작성하려는 클래스의 이름이 Child이고 상속받고자 하는 기존 클래스의 이름이 Parent라면 다음과 같이 하면 된다.

```java
class Parent { }

class Child extends Parent {
  // ...
}
```

이때 상속해주는 클래스를 '조상 클래스'라 하고 상속 받는 클래스를 '자손 클래스'라 한다.

자손 클래스는 조상 클래스의 모든 멤버를 상속받기 때문에, Child 클래스는 Parent 클래스의 멤버들을 포함한다고 할 수 있다.

만일 Parent 클래스에 age라는 정수형 변수를 멤버변수로 추가하면, 자손 클래스는 조상의 멤버를 모두 상속받기 때문에, Child 클래스는 자동적으로 age라는 멤버변수가 추가된 것과 같은 효과를 얻는다.

```java
class Parent {
  int age;
}

class Child extends Parent { }
```

반대로 자손인 Child 클래스에 새로운 멤버로 play() 메서드를 추가해보면,

```java
class Parent {
  int age;
}

class Child extends Parent {
  void play() {
    System.out.println("놀자~");
  }
}
```

Child 클래스에 새로운 코드가 추가되어도 조상인 Parent 클래스는 아무런 영향도 받지 않는다. **즉, 조상 클래스가 변경되면 자손 클래스는 자동적으로 영향을 받게 되지만, 자손 클래스가 변경되는 것은 조상 클래스에 아무런 영향을 주지 못하는 것이다**.

자손 클래스는 조상 클래스의 모든 멤버를 상속 받으므로 항상 조상 클래스보다 같거나 많은 멤버를 갖는다. 즉, 상속에 상속을 거듭할수록 상속받는 클래스의 멤버 개수는 점점 늘어나게 된다.

> - 자손 클래스는 조상 클래스의 모든 멤버를 상속받는다. (단, 생성자와 초기화 블럭은 상속되지 않는다.)
> - 자손 클래스의 멤버 개수는 조상 클래스보다 항상 같거나 많다.


### 상속 예제 코드 구현

```java
class Tv {
	boolean power;	// 전원 상태(on/off)
	int channel;	// 채널 

	void power()       {   power = !power; }
	void channelUp()   {   ++channel;      }
	void channelDown() {   --channel;      }
}

class SmartTv extends Tv {  // SmartTv는 Tv에 캡션(자막)을 보여주는 기능을 추가 
	boolean caption;		// 캡션 상태(on/off)
	void displayCaption(String text) {
		if (caption) {		// 캡션 상태가 on(true)일 때만 text를 보여 준다. 
			System.out.println(text);
		}
	}
}

class Ex7_1 {
	public static void main(String args[]) {
		SmartTv stv = new SmartTv();
		stv.channel = 10;			// 조상 클래스로부터 상속 받은 멤버 
		stv.channelUp();			// 조상 클래스로부터 상속 받은 멤버 
		System.out.println(stv.channel);
		stv.displayCaption("Hello, World");
		stv.caption = true;			// 캡션(자막) 기능를 켠다. 	       
		stv.displayCaption("Hello, World");
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
11
Hello, World
```

Tv 클래스로부터 상속받고 기능을 추가하여 SmartTv 클래스를 작성하였다. 멤버변수 caption은 캡션 기능의 상태(on/off)를 저장하기 위한 boolean형 변수이고, displayCaption(String text)은 매개변수로 넘겨받은 문자열(text)을 캡션이 켜져 있는 경우(caption의 값이 true인 경우)에만 화면에 출력한다.

자손 클래스의 인스턴스를 생성하면 조상 클래스의 멤버도 함께 생성되기 때문에 따로 조상 클래스의 인스턴스를 생성하지 않고도 조상 클래스의 멤버들을 사용할 수 있다.


### 단일 상속(single inheritance)

또 다른 객체지향 언어인 C++에서는 여러 조상 클래스로부터 상속받는 것이 가능한 '다중 상속(multiple inheritance)'을 허용하지만, 자바에서는 '단일 상속(single inheritance)'만을 허용한다.

그래서 둘 이상의 클래스로부터 상속을 받을 수 없다.

```java
class TvDVD extends Tv, DVD {   // ERROR. 조상은 하나만 허용된다.
  // ...
}
```

다중 상속을 허용하면 여러 클래스로부터 상속받을 수 있기 때문에 복합적인 기능을 가진 클래스를 쉽게 작성할 수 있다는 장점이 있지만, 클래스 간의 관계가 매우 복잡해진다는 것과, 서로 다른 클래스로부터 상속받은 멤버간의 이름이 같은 경우 구별할 수 있는 방법이 없다는 단점을 가지고 있다. 

만약 위 코드에서 Tv 클래스와 DVD 클래스 모두를 상속받을 수 있다고 가정했을 때, Tv 클래스에도 power()이라는 메서드가 있고, DVD 클래스에도 power()이라는 메서드가 있으면 선언부(이름과 매개변수)만 같고 서로 다른 내용의 두 메서드를 구별할 길이 없다. **static 메서드라면 메서드 이름 앞에 클래스의 이름을 붙여서 구별할 수 있다지만, 인스턴스 메서드의 경우 선언부가 같은 두 메서드를 구별할 수 있는 방법이 없다**. 

자바에서는 다중 상속의 이러한 문제점을 해결하기 위해 다중 상속의 장점을 포기하고 단일 상속만을 허용한다. 클래스 간의 관계가 보다 명확해지고 코드를 더욱 신뢰할 수 있게 만들어준다는 점에서 다중 상속보다 유리하다.
