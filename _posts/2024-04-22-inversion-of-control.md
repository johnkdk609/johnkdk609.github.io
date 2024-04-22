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