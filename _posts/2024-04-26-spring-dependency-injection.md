---
layout: post
title: 의존성 주입
categories: Spring-SpringBoot
date: 2024-04-26 20:11:00 +0900
---
이제 의존성 주입(dependency injection)에 대해 알아보자.

직전에 의존관계 역전 부분의 Programmer와 Computer 예시에서도 이미 의존성 주입을 일부 다뤄보았다. 그 중에서도 '생성자 의존성 주입'을 살펴보았다. Programmer에 의존성을 주입할 것인데, Computer가 필요하니 Computer을 집어넣은 것이다. 그런데 생성자를 이용해서 집어넣었다.

<br>

## 생성자 주입

우선 생성자 주입 부분을 보겠다. Programmer 클래스의 코드를 보자.

```java
package com.ssafy.di4_의존성주입;

public class Programmer {
	private Computer computer;
	
	public Programmer() {
	}
	
	// 생성자 주입
	public Programmer(Computer computer) {
		this.computer = computer;
	}
	
	public void coding() {
		System.out.println(computer.getInfo()+"으로 개발을 합니다.");
	}
}
```

위와 같이 생성자 주입을 했으니, Test 클래스를 보자.

```java
package com.ssafy.di4_의존성주입;

public class Test {
	public static void main(String[] args) {
		Desktop desktop = new Desktop();
		Laptop laptop = new Laptop();
		// 생성자 주입
		Programmer p = new Programmer(desktop);
		p.coding();
	}
}
```

위 코드의 출력 결과는 다음과 같다.

<img width="272" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/8a9fe173-7a6f-4a1e-911e-be622617c7e1">

<br>

## 설정자 주입

설정자는 Setter이다. 이 setter을 이용해서 작성을 할 것인데, ```setComputer```의 방식으로 쓰는 것은 ```computer```의 필드명이 있으니 관례적으로 쓰는 것이다.

스프링을 설계할 당시에도 다 이런식으로 쓰니까 이렇게 할 수 있게 만들어놓은 것이다. Setter의 이름은 내가 마음대로 바꿔서는 안 된다. 이 룰을 확실히 지켜야 한다.

이제 Programmer 클래스에 설정자 주입을 해보겠다. ```set```하고 <kbd>option + space</kbd>를 누르면 자동완성이 된다. Programmer 클래스의 코드를 보자.

```java
package com.ssafy.di4_의존성주입;

public class Programmer {
	private Computer computer;
	
	// 생성자 주입
	public Programmer(Computer computer) {
		this.computer = computer;
	}
	
	// 설정자 주입
	public void setComputer(Computer computer) {
		this.computer = computer;
	}
	
	public void coding() {
		System.out.println(computer.getInfo()+"으로 개발을 합니다.");
	}
}
```

Test 클래스는 다음과 같이 작성한다.

```java
package com.ssafy.di4_의존성주입;

public class Test {
	public static void main(String[] args) {
		Desktop desktop = new Desktop();
		Laptop laptop = new Laptop();
		//생성자 주입
		Programmer p = new Programmer(desktop);
		p.coding();
		
		
		//설정자 주입
		Programmer p2 = new Programmer();
		p2.setComputer(laptop);
		p2.coding();
	}
}
```

설정자 주입 방식으로 만들기 위해 ```Programmer p2 = new Programmer();```를 입력했는데 빨간 줄이 뜬다. Programmer 클래스에 기본 생성자가 없기 때문이다. 그래서 Programmer 클래스에 기본 생성자를 추가한다.

```java
public Programmer() {}
```

이것을 통해서 ```p2.setComputer(laptop);```을 하였고, 위 코드의 출력 결과는 다음과 같다.

<img width="277" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/42c98c25-5fc0-4bcf-bfe3-b4b0bc56e25d">

<br>

## 메서드 주입

메서드를 이용해서 객체를 여러 가지 던져주고, 한 번에 정의할 수 있다. 생성자에서 할 수 있는데, 바로 필요한 게 아니라서 하지 않고 나중에 추가적으로 집어넣으려 할 때 할 수 있다. (메서드 주입은 잘 사용되지는 않는다.)

설정자 주입으로는 여러 개를 동시에 주입할 수 없다.

메서드 주입 코드는 다음과 같다.

```java
package com.ssafy.di4_의존성주입;

public class Programmer {
	private Computer computer;
	
	public Programmer() {
	}
	
	//생성자 주입
	public Programmer(Computer computer) {
		this.computer = computer;
	}
	
	//설정자 주입
	public void setComputer(Computer computer) {
		this.computer = computer;
	}
	
//	//메서드 주입 (여러개를 동시에 주입하려고 할때)
//	public void init(Computer computer, Keyboard keyboard) {
//		this.computer = computer;
//		this.keyboard = keyboard;
//	}
	
	public void coding() {
		System.out.println(computer.getInfo()+"으로 개발을 합니다.");
	}
}
```

위 코드에서는 가령 ```Keyboard keyboard```가 나중에 있을 수 있다는 가정 하에 추가하였다. ```this.computer = computer;```, ```this.keyboard = keyboard```의 방식으로 한 번에 여러 개를 주입하였다. (메서드 주입은 실제로 잘 사용되지 않기 때문에 실행시키지는 않을 것이다.)

<br>

의존성을 주입하기 위해 다음과 같은 방법을 사용할 수도 있다.

가령 내가 컴퓨터 공장을 세우는 것이다. 

```java
package com.ssafy.di4_의존성주입;

public class ComputerFactory {
	public static Computer getComputer(String type) {
		if (type.equals("D")) {
			return new Desktop();
		} else if (type.equals("L")) {
			return new Laptop();
		}
		return null;
	}
}
```

위와 같이 ComputerFactory 클래스를 생성하여, getComputer로 type이 들어오면 그때마다 "D"가 들어오면 데스크탑을 만들어 주고, "L"이 들어오면 랩톱을 만들어 주고, return null이면 아무것도 안 만들어주는 것이다.

새로운 Test2 클래스를 통해 알아보자.

```java
package com.ssafy.di4_의존성주입;

import java.util.Scanner;

public class Test2 {
	public static void main(String[] args) {
		
		Scanner sc = new Scanner(System.in);
		
		Programmer p = new Programmer();
		
//		Computer computer = ComputerFactory.getComputer(sc.next());
//		p.setComputer(computer);
//		
//		p.coding();
		
		while(true) {
			p.setComputer(ComputerFactory.getComputer(sc.next()));
			p.coding();
		}
	}
}
```