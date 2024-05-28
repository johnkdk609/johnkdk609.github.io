---
layout: post
title: Spring AOP
categories: Spring-SpringBoot
date: 2024-05-26 05:33:00 +0900
---
그러면 스프링에서는 AOP를 어떻게 설정할 수 있을까?

앞서 이론을 쭉 익혔으니, 스프링에서 어떻게 쓸 수 있을지 알아보겠다.

## Advice Type

* before - target 메서드 호출 이전
* after - target 메서드 호출 이후, java exception 문장의 finally와 같이 동작
* after returning - target 메서드 정상 동작 후
* after throwing - target 메서드 에러 발생 후
* around - target 메서드의 실행 시기, 방법, 실행 여부를 결정

우선 스프링에서는 Join Point가 될 수 있는 것들은 모두 메서드들이었다. 내가 지금 target 메서드를 실행하는 시점 이전 시점이 before 이다.

그리고 after라는 것은 target 메서드 호출 이후에 finally 처럼 동작하는 것이다.

after returning, after throwing은 동시에 동작할 수 없다는 특징을 지닌다.

around은 앞의 것들을 정의한 후에 around에 몽땅 쓸 수도 있고, 아니면 각자 동작을 하게끔 쓸 수도 있는 등... 전반적인 설정이다.

<br>

## Spring AOP Proxy

스프링에서는 실제 기능이 구현된 target 객체를 호출하면, target이 호출되는 것이 아니라 advice가 적용된 Proxy 객체가 호출된다.

가령, "프로그래머야 코딩 부탁해~~" 라고 메서드를 호출했을 때, PersonProxy라는 것이 동작을 한다. 그런데 Spring에서는 이러한 것들을 자동으로 해준다.

<img width="500" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b7f0833c-e3ba-4cd6-a7fd-d5024b675d46">

* Spring AOP는 기본적으로 표준 JDK dynamic proxy를 사용한다.

* 인터페이스를 구현한 클래스가 아닌 경우 CGLIB 프록시를 사용한다.

<br>

Spring AOP 를 시작하기에 앞서 자바 언어에서 관점 지향 프로그래밍을 할 수 있게끔 하는 표준화된 AOP Framework가 있는데, AspectJ 라는 것이 있다.

AspectJ라는 것은 자바 클래스를 Aspect로 선언하는 스타일이라든지.. 등을 쓸 수 있게끔 도움을 주는 것이다.

AspectJ Weaver, AspectJ Runtime 을 깔아서 쓴다.

이제 Spring_02_AOP_2_XML 프로젝트를 생성한다. 그리고 Maven 추가를 해야 하니 마우스 우클릭 &#62; Configure &#62; Convert to Maven Project 로 바꿀 것이다

이제 pom.xml 파일을 열어서 dependencies에 우선 spring-context를 추가한다. 그리고 앞서 언급한 AspectJ Weaver, AspectJ Runtime 의존성을 추가한다.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>Spring_02_AOP_2</groupId>
  <artifactId>Spring_02_AOP_2</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <build>
    <sourceDirectory>src</sourceDirectory>
    <plugins>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.1</version>
        <configuration>
          <release>17</release>
        </configuration>
      </plugin>
    </plugins>
  </build>
  <dependencies>
  	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-context</artifactId>
	    <version>6.1.3</version>
	</dependency>
	<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
	<dependency>
	    <groupId>org.aspectj</groupId>
	    <artifactId>aspectjweaver</artifactId>
	    <version>1.9.21</version>
	</dependency>
	<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjrt -->
	<dependency>
	    <groupId>org.aspectj</groupId>
	    <artifactId>aspectjrt</artifactId>
	    <version>1.9.21</version>
	</dependency>
  </dependencies>
</project>
```

그리고, 설정파일을 쓰기 위해서 AOP namespace를 좀 추가해줘야 하는데, 이것은 Spring.io 에 가서 'XML Schemas'를 검색하고 쭉 스크롤을 내리면 'The aop Schema'와 'The context Schema'가 있다. 이 두 개를 섞어서 사용할 것이다.

그래서 프로젝트에 source 폴더를 하나 만들겠다. resources라는 이름으로 폴더를 하나 생성한다. 그리고 그 안에 applicationContext.xml 이라는 파일을 생성한다. applicationContext.xml 파일은 다음과 같다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd
		http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

</beans>
```

이제 aop라는 것과 context를 사용할 준비가 되었다.

<br>

## Spring AOP 시작하기 - xml

Spring.io &#62; Projects &#62; Spring Framework &#62; Learn &#62; 6.14 Reference Docs &#62; Search 에 "schemas"라고 검색한다. 그러면 XML Schemas 가 보이고 이것을 클릭한다. (이번 이후로 Schemas에 들어올 일은 없다.)

이제 MyAspect를 하나 만들어볼 것이다. 앞서 했던 proxy2 패키지를 복사해서 Spring_02_AOP_2_XML 프로젝트에 붙여넣는다. 그리고 패키지명을 com.ssafy.aop로 변경한다.

이제 PersonProxy는 필요 없어서 지운다. 대신에 MyAspect 클래스를 생성한다. 공통 관심사항들을 모아놓는 곳이 Aspect이다. 자바에서는 POJO 스타일을 쓴다고 했으니 이것도 무언가 상속 받아서 할 필요 없다. 순수한 자바의 형태로 할 것이다.

MyAspect 클래스의 코드는 다음과 같다.

```java
package com.ssafy.aop;

import org.aspectj.lang.ProceedingJoinPoint;

public class MyAspect {
	// 메서드 명은 중요한게 아니다. 직관적으로 보여주려고 이러한 이름을 사용함.
	public void before() {
		System.out.println("컴퓨터를 부팅한다.");
	}
	
	// 정상 수행 후
	public void afterReturning(int line) {
		System.out.println("Git에 Push 한다. : "+line+"개의 줄을....");
	}
	// 예외 발생 후
	public void afterThrowing(Throwable th) {
		System.out.println("조퇴를 한다.");
		if(th instanceof OuchException)
			((OuchException) th).handleException();
	}
	
	// 이후에 
	public void after() {
		System.out.println("하루를 마무리 한다.");
	}
	
	/////////////////////////////
	// aroud
	
	public int around(ProceedingJoinPoint pjt) {
		int line = 0;
		
		this.before();
		try {
			line = (int)pjt.proceed();
			this.afterReturning(line);
		} catch (Throwable e) {
			this.afterThrowing(e);
		}finally{
			this.after();
		}
		return line;
	}

}
```