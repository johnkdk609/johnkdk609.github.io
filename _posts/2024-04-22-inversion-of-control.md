---
layout: post
title: 의존관계역전 (Inversion of Control)
categories: Spring-SpringBoot
date: 2024-04-22 12:44:00 +0900
---
의존관계역전에 대해 알아보자.

프로그래머와 컴퓨터가 있다. 우선 새로운 워크스페이스를 하나 판다. (이때 인코딩은 전부 UTF-8로 되어 있어야 한다.) 'Create a Java Project'를 클릭하고 'Spring_01_DI_1_개념'이라는 프로젝트를 생성한다. (이번에는 Dynamic Web Project가 아니다.)

여기에 두 개가 필요한데, 클래스가 필요하다. package는 'com.ssafy.di_의존성개념'으로 지정하고 Desktop 클래스를 생성한다. (지금은 연습중이지만 실제로 할 때에는 한글을 쓰지 않는 것이 좋다.) Desktop.java의 코드는 다음과 같다.

```java
package com.ssafy.di1_의존성개념;

public class Desktop {
	// 필드명 작성
	// CPU, GPU, RAM, ... 등등
	
	// 정보를 반환
	public String getInfo() {
		return "데스크톱";
	}
}
```

<br>

이번에는 Programmer 클래스를 만든다. Programmer.java의 코드는 다음과 같다.

```java
package com.ssafy.di1_의존성개념;

public class Programmer {
	private Desktop desktop;
	
	public Programmer() {
		// 프로그래머 한 명이 새로 들어왔으면 컴퓨터 하나 사서 제공해야함.!
		this.desktop = new Desktop();
	}
	
	public void coding() {
		System.out.println(desktop.getInfo() + "으로 개발을 합니다.");
	}
}
```

프로그래머 한 명이 회사에 들어오면 컴퓨터를 하나 사서 제공하는 코드를 위와 같이 작성하였다. 그리고 Desktop 클래스로 생성한 객체의 메서드(```getInfo()```)를 사용했다.

<br>

그러고 나서 Test 클래스를 하나 생성한다.

```java
package com.ssafy.di1_의존성개념;

public class Test {
	public static void main(String[] args) {
		// 프로그래머가 데스크톱에 대한 의존성을 가지고 있음.
		Programmer p = new Programmer();
		
		p.coding();
	}
}
```

위 코드를 실행하면 콘솔에 다음과 같이 출력된다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/bf96f960-e2a9-4ad4-9eb7-923239aa1959" width="350px" />

프로그래머가 데스크톱에 대한 의존성을 가지고 있다. 프로그래머는 컴퓨터가 꼭 필요하다. 프로그래머를 만들기 위해서는 컴퓨터를 꼭 만들어서 줘야 한다. 이를 "의존성이 있다"라고 표현한다.

<br>

<u>ClassA 객체가 어떤 일을 처리하기 위해서 ClassB 객체의 도움을 받아야만 일을 처리할 수 있다면 <b>'ClassA는 ClassB에 의존한다.'</b> 라고 표현한다.</u>

<br>

프로그래머가 일을 처리하기 위해서는 컴퓨터가 필요하니까, 내가 컴퓨터를 집어 넣어줬다.

Programmer가 Desktop 클래스에 의존성을 가지고 있는데, 내가 Laptop으로도 일을 하고 싶어졌다. 그러면 코드를 고쳐야 하는데 몇 군데를 고쳐야 할까?

다음 두 군데를 고쳐야 한다.

```java
public class Programmer {
	private Desktop computer;	// 이 부분 고쳐야 함

	public Programmer() {
		computer = new Desktop();	// 이 부분 고쳐야 함
	}

	public void coding() {
		System.out.println(computer.getInfo() + "으로 개발을 합니다.");
	}
}
```

그리고 Laptop 클래스의 코드는 다음과 같을 것이다.

```java
public class Laptop {
	// 필요한 필드...

	// 해당 컴퓨터 정보를 반환
	public String getInfo() {
		return "랩톱";
	}
}
```

이렇게 코드를 수정하기 전에, 먼저 수행해야 하는 것이 있다. 

<br>

## 객체생성 의존성 제거

객체생성 의존성을 제거한다는 것이 무슨 뜻일까?

현재 코드에서는 프로그래머가 한 명 만들어질 때마다 ```new Desktop()```을 통해서 만들어줘야 했다. 그런데 이제는 Programmer가 객체를 생성하지 않겠다는 것이다.

다음과 같이 코드를 수정한다.

```java
package com.ssafy.di2_객체의존성제거;

public class Programmer {
	private Desktop desktop;
	
	//객체 생성 의존성 제거
	public Programmer(Desktop desktop) {
		//직접 생성하는 것이 아닌, 데스크톱을 인자로 받아서 제공하겠다.
		this.desktop = desktop;
	}
	
	public void coding() {
		System.out.println(desktop.getInfo()+"으로 개발을 합니다.");
	}
}
```

위와 같이 ```public Programmer(Desktop desktop)```와 같이 수정하여 Desktop을 인자로 받아서 던져준다. 이제 내가 데스크톱을 생성하는 것이 아니고, 데스크톱을 지정해주는 것이다.

이렇게 더 이상 프로그래머는 의존성에 대한 부담이 없다. 프로그래머 한 명이 올 때마다 컴퓨터를 사줘야 하는 상황이 아닌 것이다.

이제 외부에서 생성한 데스크톱을 집어넣어줄 것이다.

<br>

이제 테스트를 해보자.

```java
package com.ssafy.di2_객체의존성제거;

public class Test {
	public static void main(String[] args) {
		// 의존관계역전을 맛 본 것.
		
		// 스프링 컨테이너가 이러한 것들을 해준다.
		Desktop desktop = new Desktop();
		Programmer p = new Programmer(desktop);
		
		p.coding();
	}
}
```

출력 결과는 다음과 같다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b82fb435-21dc-4694-9263-53d80736db1d" width="250px" />

밴다이어그램으로 보자면, 원래는 프로그래머가 데스크톱을 포함하고 있었다. 그런데 이제는 그렇지 않다. 어딘가 외부에서 만든 데스크톱을 프로그래머에 집어넣어준 것이다.

이러한 것을 <b>의존관계 역전</b>이라고 한다. 스프링은 이렇게 ```new ~~~``` 하는 것들을 전부 모아서 "이거 해줘"라고 등록만 해두면 다 알아서 만들어서 의존성을 주입해준다. 스프링 컨테이너가 이러한 역할을 수행한다.

```new ~~~```를 하지 않고 만들어진 것을 넣어주고, 이렇게 넣어주는 행위를 '<b>의존성 주입</b>'이라고 한다.

<br>

## 타입 의존성 제거

이번에는 타입 의존성 제거에 대해 알아보겠다.

내가 Laptop으로 바꾸고 싶은 상황이다. 우선 Laptop 클래스의 코드는 다음과 같다.

```java
package com.ssafy.di3_타입의존성제거;

public class Laptop implements Computer {
	// 필드명 작성
	// CPU, GPU, RAM, ... 등등
	
	// 정보를 반환
	public String getInfo() {
		return "랩톱";
	}
}
```

그러면 Programmer 클래스에 대해 다시 생각해보자. 기존의 Programmer 클래스 코드에서 Desktop을 Laptop으로 바꾸기만 하면 되는 것일까?

이렇게 하면 굉장히 번거롭다. 이런 것을 <b>'강한 결합'</b>이라고 한다. Programmer은 Desktop을 쓸 수도 있어야 하고, Laptop을 사용할 수도 있어야 한다. 

그래서 이에 반대 개념인 <b>'느슨한 결합(loose coupling)'</b>이 필요하다. Desktop도 쓸 수 있어야 하고, Laptop도 쓸 수 있어야 한다. 너무 하나의 타입에만 의존성을 가지고 있는 것이 아니라, 다양하게 쓸 수 있어야 한다. 이때 <b>다형성을 이용해야 한다.</b>

인터페이스의 개념을 쓰면 된다. Computer은 웬만하면 다 쓰니까, Computer 인터페이스를 이용해서 그것을 구현하고 있는 Desktop, Laptop을 만들고 사용자는 컴퓨터만 사용하겠다고 하면 그 안에 내용물이 Desktop인지 Laptop인지는 별로 중요하지 않게 되는 것이다.

<u>역할과 구현을 구분하는 것</u>이다. 사용자는 Desktop이나 Laptop에 의존하고 있으면 안 된다.

우선 Computer 인터페이스를 만든다.

```java
package com.ssafy.di3_타입의존성제거;

public interface Computer {
	String getInfo();
}
```

그리고 수정한 Programmer 클래스의 코드는 다음과 같다.

```java
package com.ssafy.di3_타입의존성제거;

public class Programmer {
	private Computer computer;
	
	// 객체 생성 의존성 제거
	public Programmer(Computer computer) {
		// 직접 생성하는 것이 아닌, 데스크톱을 인자로 받아서 제공하겠다.
		this.computer = computer;
	}
	
	public void coding() {
		System.out.println(computer.getInfo()+"으로 개발을 합니다.");
	}
}
```

이제 Programmer에서 인자로 ```Computer computer```를 가져온다.

Test 클래스의 코드는 다음과 같다.

```java
package com.ssafy.di3_타입의존성제거;

public class Test {
	public static void main(String[] args) {
		
		Desktop desktop = new Desktop();
		Programmer p = new Programmer(desktop);
		
		p.coding();
		
		Laptop laptop = new Laptop();
		Programmer p2 = new Programmer(laptop);
		
		p2.coding();
	}
}
```

출력 결과는 다음과 같다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/fe2bd419-0f6d-4d39-9a7b-16fbc35fe304" width="260px">

이렇게 느슨한 결합을 하면 얼마든지 손쉽게 갈아끼울 수 있다는 장점이 있다.