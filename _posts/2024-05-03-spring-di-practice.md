---
layout: post
title: Spring DI 연습
categories: Spring-SpringBoot
date: 2024-05-03 18:31:00 +0900
---
의존성을 주입하기 위해서는 XML, Annotation, JavaConfig의 방법들을 사용할 수 있다.

우선 XML을 사용하는 방법을 이어서 알아보자.

의존성을 주입하기 위해서 applicationContext.xml을 활용했었다. 이번에는 Test2를 해보기 위해 applicationContext2.xml을 만들어보겠다.

resources 폴더에 applicationContext2.xml을 생성하고 기존 applicationContext2.xml의 코드를 복사 붙여넣기 한다. applicationContext2.xml 의 코드는 다음과 같다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

		<!-- 풀패키지명 -->
		<bean class="com.ssafy.di.Desktop" id="desktop"></bean>
<!-- 	<bean class="com.ssafy.di.Programmer" id="programmer">
			<constructor-arg ref="desktop"></constructor-arg>
		</bean> -->
		<bean class="com.ssafy.di.Programmer" id="programmer">
			<!-- setter의 이름 -->
			<property name="computer" ref="desktop"></property>
		</bean>
</beans>
```

그리고 이제 Test2를 보자. Test2의 코드는 다음과 같다.

```java
package com.ssafy.di;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

public class Test2 {
	public static void main(String[] args) {

		ApplicationContext context = new GenericXmlApplicationContext("applicationContext2.xml");

		Programmer p = context.getBean("programmer", Programmer.class);
		
		p.coding();
		
	}
}
```

위 코드의 실행 결과는 다음과 같다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7bc45831-8fae-42e6-ba99-0052ff15b4e8" width="300px" />

"데스크톱으로 개발을 합니다."가 알아서 튀어나온다. 조립을 applicationContext2.xml에서 한 것이다. 위에서 constructor-arg 방식으로 할 수도 있고, 또 property로 할 수도 있다. (property로 한다면 설정자를 넣어야 한다.) 그리고 참고할 ref는 "Desktop"으로 하였다.

<br>

정리하자면 다음과 같다.

### 의존성 주입 (생성자)

constructor-arg를 이용하여 의존성 주입. &#60;ref&#62;, &#60;value&#62; 와 같이 하위 태그를 이용하여 설정하거나, 속성을 이용하여 설정한다.

사용 예시는 다음과 같다.

```xml
<bean class="com.ssafy.di.Desktop" id="desktop"></bean>

<bean class="com.ssafy.di.Programmer" id="programmer">
	<constructor-arg ref="desktop"></constructor-arg>
</bean>
```

```java
package com.ssafy.di;

public class Programmer {
	private Computer computer;

    // 기본 생성자
	public Programmer() {
	}

	// 생성자를 이용한 의존성 주입
	public Programmer(Computer computer) {
		this.computer = computer;
	}

}
```

<br>

### 의존성 주입 (설정자)

setter를 이용하여 의존성 주입. &#60;ref&#62;, &#60;value&#62; 와 같이 하위 태그를 이용하여 설정하거나, 속성을 이용하여 설정할 수 있다.

```xml
<bean class="com.ssafy.di.Desktop" id="desktop"></bean>

<bean class="com.ssafy.di.Programmer" id="programmer">
    <!-- setter의 이름 -->
    <property name="computer" ref="desktop"></property>
</bean>
```

```java
package com.ssafy.di;

public class Programmer {
	private Computer computer;

    // 기본 생성자
	public Programmer() {
	}

	// 설정자(setter)을 이용한 의존성 주입
	public void setComputer(Computer computer) {
		this.computer = computer;
	}

}
```

<br>