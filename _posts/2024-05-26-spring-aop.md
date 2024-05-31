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

}
```

그리고 Programmer 클래스의 코드는 다음과 같이 설정한다.

```java
package com.ssafy.aop;

import java.util.Random;

public class Programmer implements Person {
	// 필드는 과감히 생략!

	// 프로그래머의 일상
	public int coding() {
		System.out.println("열심히 코드를 작성한다."); // 핵심관심사항
		if (new Random().nextBoolean())
			throw new OuchException();
		return (int)(Math.random() * 100) + 1;
	}

}
```

int 자료형을 반환해야 하기 때문에, int형으로 parse를 해준다. 그리고 1을 더해서 1 에서 100 사이의 값이 나오게 하였다.

<br>

이제 등록할 것이다.

다시 설정 파일 applicationContext.xml로 넘어간다. 우선 빈 등록을 해야 한다. 클래스 이름 "com.ssafy.aop.Programmer"으로, 그리고 id는 "programmer"로 등록을 하겠다.

또, "com.ssafy.aop.MyAspect"를 클래스로 등록하고 id는 "myAspect"로 등록을 하겠다.

```xml
<bean class="com.ssafy.aop.Programmer" id="programmer"></bean>
<bean class="com.ssafy.aop.MyAspect" id="myAspect"></bean>
```

결국에는 Aspect, Person, Programmer 등이 전부 다 컨테이너에 빈으로 등록이 되어 있어야 자동으로 생성하여 쓸 수 있는 것이다.

그리고 여기서 aop:config를 쓸 것이다. pointcut을 쓸 것이다. pointcut을 적용한 applicationContext.xml의 코드는 다음과 같다.

aop:config는 aop에 대한 설정들을 쓰는 것인데, aop:aspect 위에 aop:pointcut을 지정했으면 이것은 전역으로 설정을 한 것이고, 만약 aop:aspect 아래에 aop:pointcut을 넣으면 이것은 이번 aspect에서만 쓰는 pointcut을 지정한 것으로 이해하면 된다.

```xml
<aop:config>
    <aop:pointcut expression="execution(* *(..))" id="mypt"/>
    <aop:aspect ref="myAspect">
        <!-- 메서드 명을 작성하자! -->
        <aop:before method="before" pointcut-ref="mypt"/>
        <aop:after-returning method="afterReturning" pointcut-ref="mypt" returning="line"/>
        <aop:after-throwing method="afterThrowing" pointcut-ref="mypt" throwing="th" />
        <aop:after method="after" pointcut-ref="mypt"/>
    </aop:aspect>
</aop:config>
```

위 콛그를 보면 aspect은 위에서 설정한 "myAspect"를 가져다 쓰겠다는 것이고, aop:before에는 method 명을 쓴다. 그리고 이 안에서 pointcut을 참조를 할 것인데, "mypt"라는 것을 통해서 모든 메서드에 before라는 것을 실행시키겠다는 것이다.

그리고 after-returning의 메서드명은 "afterReturning"이고, pointcut의 참조값으로는 "mypt", 반환값으로는 "line"을 반환할 것이다.

이렇게 저장을 마무리 하고, 다시 Test로 넘어온다. Test 클래스의 코드는 다음과 같다.

```java
package com.ssafy.aop;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

public class Test {
	public static void main(String[] args) {
		
		ApplicationContext context = new GenericXmlApplicationContext("applicationContext.xml");
		
		Person p = context.getBean("programmer", Person.class);
		
		try {
			p.coding();
		}catch (Exception e) {
		}
	}
}
```

Person을 가져올 것인데, 이름은 "programmer"로 가져올 것이고, Person.class로 클래스를 지정하였다. 만약 Programmer.class로 입력하면 예외가 터진다. 인터페이스를 구현한 클래스가 아닌 경우 CGLIB 프록시를 사용한다고 했었는데, 이것 때문에 그런 것이다. 그래서 인터페이스명인 Person.class로 가져왔다.

그리고 try ~ catch 구문을 사용해서 예외를 처리하였다. Test 클래스의 실행 결과는 다음과 같다.

<img width="386" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/bad45ea1-9cc7-43f9-8488-d8f32b8fd58a">

<br>

다음으로 around를 작성해볼 것이다.

MyAspect 클래스의 코드에 다음과 같이 around를 위한 부분을 추가한다.

```java
package com.ssafy.aop;

import org.aspectj.lang.ProceedingJoinPoint;

public class MyAspect {
	//메서드 명은 중요한게 아니야! 직관적으로 보여주려고 이러한 이름을 사용함.
	public void before() {
		System.out.println("컴퓨터를 부팅한다.");
	}
	
	//정상 수행 후
	public void afterReturning(int line) {
		System.out.println("Git에 Push 한다. : "+line+"개의 줄을....");
	}
	//예외 발생 후
	public void afterThrowing(Throwable th) {
		System.out.println("조퇴를 한다.");
		if(th instanceof OuchException)
			((OuchException) th).handleException();
	}
	
	//이후에 
	public void after() {
		System.out.println("하루를 마무리 한다.");
	}
	
	/////////////////////////////
	//aroud
	
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

이제 applicationContext.xml의 코드를 수정해야 한다. 수정된 applicationContext.xml의 코드는 다음과 같다.

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


	<bean class="com.ssafy.aop.Programmer" id="programmer"></bean>
	<bean class="com.ssafy.aop.MyAspect" id="myAspect"></bean>

<!-- 
	<aop:config>
		<aop:pointcut expression="execution(* *(..))" id="mypt"/>
		<aop:aspect ref="myAspect">
			<aop:before method="before" pointcut-ref="mypt"/>
			<aop:after-returning method="afterReturning" pointcut-ref="mypt" returning="line"/>
			<aop:after-throwing method="afterThrowing" pointcut-ref="mypt" throwing="th" />
			<aop:after method="after" pointcut-ref="mypt"/>
		</aop:aspect>
	</aop:config> -->
	<aop:config>	
		<aop:pointcut expression="execution(* *(..))" id="mypt"/>
		<aop:aspect ref="myAspect">
			<aop:around method="around" pointcut-ref="mypt"/>
		</aop:aspect>
	</aop:config>

</beans>
```

이제는 그냥 "myAspect" 에다가 aop:around를 쓸 것인데 메서드명은 "around"이다 라고 하겠다. 이것 또한 pointcut-ref를 "mypt"로 해서 바로 쓰면 된다.

이 상태에서 Test를 실행하면 동일하게 동작한다. 달라진 게 없다. 단지 사용 방법이 조금 달라진 것이다.

<img width="392" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/58551788-97db-4744-8e00-013e240a6f0e">

<br>

## Spring AOP 시작하기 - Annotation

이제 어노테이션 방식으로 AOP를 사용하는 것을 알아보겠다. 우선 앞서 사용한 Spring_02_AOP_2_XML을 복사 붙여넣기 하고 이름을 Spring_02_AOP_3_Annotation으로 명명한다.

이것을 하기 위해서는 @Aspect를 써야 한다.

```xml
<aop:aspectj-autoproxy/>
```

위와 같은 설정이 들어가야 한다. applicationContext.xml로 들어가서 다음과 같이 수정한다.

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

	<!-- XML 기반 구성으로 @AspectJ를 지원 활성화 하려면 작성해야함. -->
	<aop:aspectj-autoproxy/>
	<context:component-scan base-package="com.ssafy.aop"></context:component-scan>

</beans>
```

(만약 xml 파일에서 계속 빨간 줄이 뜬다면, Window &#62; Preferences 에 들어가서 'Download Aritfact Javadoc'을 체크한다. 그러면 없어질 수 있다. 빨간 줄이 거슬려서 하는 것이지 빨간 줄이 있다고 문제가 있는 것은 아니다.)

이제 MyAspect 부분만 조금 고치면 된다. 이제 MyAspect를 컴포넌트로 등록해야 한다.

```java
package com.ssafy.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class MyAspect {
	
	@Pointcut("execution(* com.ssafy.aop.*.coding())")
	public void mypt() {}
	
	
//	@Before("mypt()")
	public void before() {
		System.out.println("컴퓨터를 부팅한다.");
	}
	
//	@AfterReturning(value = "mypt()", returning = "line")
	public void afterReturning(int line) {
		System.out.println("Git에 Push 한다. : "+line+"개의 줄을....");
	}
	
//	@AfterThrowing(value = "mypt()", throwing = "th")
	public void afterThrowing(Throwable th) {
		System.out.println("조퇴를 한다.");
		if(th instanceof OuchException)
			((OuchException) th).handleException();
	}
	
//	@After("mypt()")
	public void after() {
		System.out.println("하루를 마무리 한다.");
	}
	
	/////////////////////////////
	//aroud
	@Around("mypt()")
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

수정된 위 코드를 보면, ```@Component```를 붙여서 빈 등록을 하고, ```@Aspect```를 붙여서 동작을 시켰다.

Pointcut을 써야 하기 때문에 ```@Pointcut("execution(* com.ssafy.aop.*.coding())")```와 같이 입력하였다. 이것은 com.ssafy.aop 패키지 안에 있는 모든 클래스가 가지고 있는 인자가 하나도 없는 coding() 메서드가 동작할 때 이러한 것들을 적용하겠다는 것이다.

```java
@Pointcut("execution(* com.ssafy.aop.*.coding())")
public void mypt() {}
```

는 Pointcut을 "mypt"로 등록한 것이다. 이전에 XML 방식에서 살펴봤던 방식과 정확히 동일하게 동작하는 것이다. "mypt"라는 이름으로 꺼내쓸 수 있게 한 것이다.