---
layout: post
title: Spring Container Build
categories: Spring-SpringBoot
date: 2024-04-28 22:26:00 +0900
---
Spring Container Build에 대해서 알아보겠다.

## Spring IoC Container

스프링에서 핵심적인 역할을 하는 객체를 <b>Bean</b>이라고 하며, <b>Spring Container는 Bean의 인스턴스화 조립, 관리의 역할, 사용 소멸에 대한 처리를 담당한다.</b> '인스턴스화 조립'이란 ```new ~~~``` 해서 인스턴스를 생성해 의존성을 알아서 주입해준다는 것이다. '사용 소멸'이란 생명주기(LifeCycle)에 관여한다는 것이다.

* BeanFactory (interface)
    - 프레임워크 설정과 기본 기능을 제공하는 컨테이너
    - 모든 유형의 객체를 관리할 수 있는 메커니즘 제공

* ApplicationContext (interface)
    - BeanFactory 하위 인터페이스
    - 이벤트 처리, 국제화용 메세지 처리 등 다양한 기능 제공

* WebApplicationContext (interface)
    - 웹 환경에서 Spring을 사용하기 위한 기능이 추가됨
    - 대표적인 구현 클래스로 XmlApplicationContext가 있음

BeanFactory를 그대로 받아서 만든 ApplicationContext, 그리고 그 ApplicationContext에서 좀 더 나아가서 만든 WebApplicationContext 등이 있다. 이런 것들을 가져와서 처리할 것이다.

<br>

## 스프링 설정 정보 (Spring configuration metadata)

그래서 스프링 설정 정보를 쓸 수 있어야 한다.

스프링 설정 정보란 어플리케이션 작성을 위해 생성할 Bean과 설정 정보, 의존성 등의 방법을 나타내는 정보이다. 이러한 설정 정보를 작성하는 방법에는 XML 방식, Annotation 방식, Java Config 방식이 있다.

(나는 XML 방식과 Annotation 방식을 중점적으로 다뤄볼 것이다. 최신 트렌드는 Java Config 방식인데, 이는 Spring Boot를 다룰 때 알아볼 것이다.)

<br>

이제 새로운 자바 프로젝트 Spring_01_DI_2_XML 를 생성한다.

프로젝트를 스프링을 쓸 수 있게끔 넣어야 하는데, 넣는 방법은 두 가지가 있었다.

1. 관련된 jar 파일을 가지고 와서 전부 넣는 방법
2. Maven Project로 변경한 뒤 dependency에 추가하는 방법

이번에는 스프링 컨테이너를 빌드하기 위해 Maven Project로 변경하고, pom.xml에 Spring Context 의존성을 추가하겠다.

자바 프로젝트 Spring_01_DI_2_XML 에 우클릭 하고 Configure에 가서 Convert to Maven Project를 클릭하고 finish를 누른다.

이제 MvnRepository 사이트에 가서 Spring Context 6.13 버전을 가져오겠다. 복사를 하고, 프로젝트의 pom.xml의 dependencies에 추가한다. 이렇게 등록하는 순간 Maven Dependencies에 쭉 등록이 된다.

<br>

Spring Container를 빌드하기 위해서는 총 3가지 방식이 있는데, 이 중에서 첫 번째 방식인 XML 방식을 이용해서 등록해보겠다.

프로젝트에 마우스 우클릭을 하고 Source Folder을 클릭한다. 폴더 명은 "resources"로 한다. 그리고 resources 폴더에 우클릭을 하고 Other에 가서 XML을 검색하여, XML File을 선택하고 Next를 누른 다음, 위치는 resources로, File name은 "applicationContext.xml"로 한다. (관례적으로 하는 이름)

이러한 XML 파일을 쓰기 위해서 기본적으로 채워야 하는 부분들이 있는데, 이를 우리가 다 외울 수는 없다. Spring.io 사이트에 가서, Spring Framework 항목에서 "LEARN"을 클릭한다. 그리고 Reference Doc. 을 클릭하고, "XML"을 검색한다. 그리고 쭉 스크롤을 내리다가 The context Schema 부분 코드를 복사한다. 그리고 applicationContext.xml에 전부 붙여넣기 한다.

그리고 설정 파일을 만들어놓은 상태에서 빈으로 등록을 하겠다는 것을 다음과 같이 입력하여 설정한다.

```xml
<bean class="~~~" id="~~~"></bean>
```

그래서 만들어진 applicationContext.xml의 코드는 다음과 같다.

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
		<bean class="com.ssafy.di.Programmer" id="programmer"></bean>
</beans>
```

풀 패키지명을 작성해야 하는데, 여기서는 "com.ssafy.di.Desktop"의 방식으로 풀 패키지명을 입력했다. <u>id는 Bean의 이름</u>이다. 빈의 이름을 내가 등록하지 않으면, 가장 앞글자를 소문자로 바꾼 이름으로 등록이 된다. 이 경우에는 Desktop의 맨 앞글자를 소문자로 바꾼 "desktop"으로 등록이 될 것이다.

<br>

이제 Test 클래스를 만들겠다. Test 클래스의 코드는 다음과 같다.

```java
package com.ssafy.di;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

public class Test {
	public static void main(String[] args) {

		ApplicationContext context = new GenericXmlApplicationContext("applicationContext.xml");

		System.out.println("1~~~~");
		Programmer p = (Programmer) context.getBean("programmer");
		// 기본 설정은 getBean 할때 생성하는 것이 아닌 컨테이너를 빌드할때 빈들이 생성이 된다.
		Desktop d = context.getBean("desktop", Desktop.class);
		p.setComputer(d);
		
		System.out.println("2~~~~~");
		
		p.coding();
		
		
		Desktop d2 = context.getBean("desktop", Desktop.class);
		
		System.out.println(d == d2); // true : 컨테이너는 기본적으로 싱글턴으로 관리를 한다.

	}
}
```

우선 ```ApplicationContext context = new GenericXmlApplicationContext("applicationContext.xml");```에서 괄호에는 경로를 입력해준다. 여기에 "applicationContext.xml"을 입력하면 애초에 resource 폴더는 source 폴더와 동급에 위치해 있기 때문에 바로 접근할 수 있다. 이렇게 함으로써 스프링 컨테이너를 빌드해서 쓸 수 있다. (사실 앞으로는 이렇게 할 일은 없을 것이다. 나중에는 내부적으로 MVC에서 다 해준다.)

```context.getBean("programmer");```는 이전에 등록한 빈의 이름을 작성하는 것이다. 여기에 "programmer"라고 쓸 수 있다. ```.getBean()```의 반환 타입은 Object이므로 Programmer로 형변환 해줘야 한다.

```Desktop d = context.getBean("desktop", Desktop.class);```와 같이 애초에 이것은 Desktop 이라는 설정을 이어서 쓰면 따로 형변환을 하지 않고도 가져옴과 동시에 형변환을 해서 집어넣을 수 있다.

```p.setComputer(d);```를 하고 ```p.coding();```을 하고 실행하면 다음과 같다.