---
layout: post
title: 익명 클래스(anonymous class)
description: Java의 정석 기초편 ch. 7 객체지향 프로그래밍
categories: Java
date: 2023-04-23 14:54:00 +09:00
---
익명 클래스(anonymous class)는 특이하게도 다른 내부 클래스들과는 달리 이름이 없다. 클래스의 선언과 객체의 생성을 동시에 하기 때문에 단 한 번만 사용될 수 있고 오직 하나의 객체만을 생성할 수 있는 일회용 클래스이다.

```java
new 조상클래스이름() {
  // 멤버 선언
}

또는

new 구현인터페이스이름() {
  // 멤버 선언
}
```

이름이 없기 때문에 생성자도 가질 수 없으며, 조상 클래스의 이름이나 구현하고자 하는 인터페이스의 이름을 사용해서 정의하기 때문에 하나의 클래스로 상속받는 동시에 인터페이스를 구현하거나 둘 이상의 인터페이스를 구현할 수 없다. 오로지 **단 하나의 클래스를 상속받거나 단 하나의 인터페이스만 구현할 수 있다**. 

```java
class Ex7_17 {
	Object iv = new Object(){ void method(){} };          // 익명 클래스 
	static Object cv = new Object(){ void method(){} };   // 익명 클래스 

	void myMethod() {
		Object lv = new Object(){ void method(){} };  // 익명 클래스 
	}
}
```

위의 예제는 단순히 익명 클래스의 사용 예를 보여준 것이다. 이 예제를 컴파일 하면 다음과 같이 4개의 클래스 파일이 생성된다.

**Ex7_17.class**\
**Ex7_17$1.class**    <- 익명 클래스\
**Ex7_17$2.class**    <- 익명 클래스\
**Ex7_17$3.class**    <- 익명 클래스


### 익명 클래스(anonymous class) 예제 코드

```java
import java.awt.*;
import java.awt.event.*;

class Ex7_18 {
	public static void main(String[] args) {
		Button b = new Button("Start");
		b.addActionListener(new EventHandler());
	}
}

class EventHandler implements ActionListener {
	public void actionPerformed(ActionEvent e) {
		System.out.println("ActionEvent occurred!!!");
	}
}
```

이 예제를 실행하면 아무것도 화면에 나타나지 않은 채 종료된다. 단지 익명 클래스로 변환하는 예를 보여주기 위한 것일 뿐이기 때문이다.


```java
import java.awt.*;
import java.awt.event.*;

class Ex7_19 {
	public static void main(String[] args) {
		Button b = new Button("Start");
		b.addActionListener(new ActionListener() {
				public void actionPerformed(ActionEvent e) {
					System.out.println("ActionEvent occurred!!!");
				}
			} // 익명 클래스의 끝 
		);
	} // main의 끝 
} 
```

예제 7-18을 익명 클래스를 이용해서 변경한 것이 예제 7-19이다. 먼저 두 개의 독립된 클래스를 작성한 다음에, 익명 클래스를 이용하여 변경하면 보다 쉽게 코드를 작성할 수 있을 것이다.
