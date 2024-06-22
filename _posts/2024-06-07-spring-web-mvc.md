---
layout: post
title: Spring Web MVC
categories: Spring-SpringBoot
date: 2024-06-07 19:07:00 +0900
---
프레임워크를 배운다는 것은, 프레임워크가 어떻게 만들어져 있는지를 공부하는 것이 아니라, 만들어진 프레임워크를 사용하는 방법에 대해서 익히는 것이다.

스프링에서는 Web MVC 를 어떻게 사용하고 있는지 살펴보자.

## Spring Web MVC

* Servlet API를 기반으로 구축된 Web Framework
* 정식 명칭은 Spring Web MVC 이지만, Spring MVC로 주로 알려져 있음
* Spring Framework 이 제공하는 DI, AOP 뿐 아니라, WEB 개발을 위한 기능을 제공
* DispatcherServlet(Front-Controller)를 중심으로 디자인 되었으며, View Resolver, Handler Mapping, Controller 와 같은 객체와 함께 요청을 처리하도록 구성됨

내가 만약 스프링을 하려면, 스프링 내부적으로 Spring Boot, Spring Data, Spring Security 등.. 매우 많다. Spring Web MVC는 그 중의 하나이다.

<br>

## Spring MVC 구성요소

* DispatcherServlet → 클라이언트 요청 처리 (요청 및 처리 결과 전달)
* HandlerMapping → 요청을 어떤 Controller 가 처리할 지 결정
* Controller → 요청에 따라 수행할 메서드를 선언하고, 요청처리를 위한 로직 수행 (비즈니스 로직 호출)
* ModelAndView → 요청처리를 하기 위해서 필요한 혹은 그 결과를 저장하기 위한 객체
* ViewResolver → Controller에 선언된 view이름을 기반으로 결과를 반환할 View를 결정
* View → 응답화면 생성

모든 요청과 모든 응답은 DispatcherServlet으로 시작해서 DispatcherServlet으로 끝난다고 보면 된다.

ModelAndView는 컨트롤러를 거치고 나온 객체인데, 요청 처리를 위해서 필요한 혹은 그 결과를 저장하기 위한 객체 즉, '<b>바구니</b>'이다. 이 바구니 안에 필요한 데이터들을 몽땅 담아서 '어디, 어떤 view로 보내주세요' 하고 이름을 넣는 바구니인 것이다.

View는 결국 응답 화면을 생성하는 JSP 이다.

<br>

## Spring MVC - 요청 처리 흐름

그래서 이 요청과 흐름은 이러한 방식으로 이뤄져 있다.

<img width="632" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/305f0770-fda2-4cc5-b6a4-5080587e3e98" alt="https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:rte2:ptl:spring_mvc_architecture">

1. Client의 요청이 들어오면 DispatcherServlet이 가장 먼저 요청을 받는다.
2. HandlerMapping이 요청에 해당하는 Controller를 return한다.
3. Controller는 비즈니스 로직을 수행(호출)하고 결과 데이터를 ModelAndView에 반영하여 return한다.
4. ViewResolver는 view name을 받아 해당하는 View 객체를 return한다.
5. View는 Model 객체를 받아 rendering한다.

그런데 HandlerMapping에서 나온 결과가 Controller라 되어 있는데, 인터넷을 검색해보면 한 depth가 더 있다. HandlerAdapter라는 것이 있다. HandlerApdater를 호출하고 그 HandlerAdapter가 Controller(핸들러)를 호출한다.

그래서 HandlerMapping이라는 것은 결국 어떤 컨트롤러에 가야 하는지 알려주는 것이다. 그래서 DispatcherServlet까지 다시 돌아왔다.

DispatcherServlet은 이제 사용자가 어떤 요청을 보냈는데, 어떤 컨트롤러가 처리하면 될지에 대한 정보까지 있다. 이것을 통해서 해당 컨트롤러에게 실제로 요청을 보낸다. 그러면 컨트롤러는 내부적으로 서비스를 호출하고, 리포지토리를 통해서 필요하다면 DB까지 다녀와서 처리를 한다. (Model과 소통을 하는 것이다.)

그러고 나서 Controller에는 ModelAndView라는 객체가 튀어 나오는데, Model이라고 하는 파트에서는 데이터에서 필요한 정보들을 저장할 것이고, View라는 것에서는 어떤 View에 가면 될지를 알려줘야 하니까 어떤 view로 가면 되는지에 대한 view 이름을 담아서 'ModelAndView'라는 객체를 DispatcherServlet에게 돌려준다.

이제 DispatcherServlet은 ModelAndView 객체에서 'view'의 이름만 알고 있다. 그 view의 이름을 ViewResolver에게 보낸다. ViewResolver는 view 이름을 가지고 '아 이건 어떤 경로에 있고 어디에 가면 확장자는 어떤 것이고 ~'를 다 가지고 있다. View 이름만을 가지고 확장자를 붙이는 것이다.

예전에 'WEB-INF/regist.jsp'와 같은 방식으로 직접 경로를 명시했다면, 이제는 'view 이름은 regist 야' 까지만 ModelAndView에 담아서 보내는 것이다. 그러면 ViewResolver라는 것이 regist라고 하는 이름을 보고 실제 경로인 WEB-INF/ 뒤에 붙이고, 확장자인 .jsp를 붙여서 DispatcherServlet에게 돌려준다.

이제 DispatcherServlet에는 어떤 모델, 어떤 정보를 가지고 있는지와, 그리고 view 파일이 어디에 가면 있는지를 다 알고 있다. 그래서 View 파일로 넘어간다.

그러면 JSP를 통해서 모델에서 받은 객체로 쭉 작성을 해버리는 것이다. 작성이 끝났으면 response로 DispatcherServlet에게 보내고, 다시 그 response를 client에게 쏴버린다.

<br>

위와 같은 일련의 과정을 다시 정리하면 다음과 같다.

1. 클라이언트 요청이 들어오면 DispatcherServlet이 받는다.
2. HandlerMapping이 어떤 Controller가 요청을 처리할지 결정한다.
3. DispatcherServlet은 Controller에 요청을 전달
4. Controller는 요청을 처리한다.
5. 결과(요청 처리를 위한 data, 결과를 보여줄 view의 이름)를 ModelAndView에 담아 반환
6. ViewResolver에 의해서 실제 결과를 처리할 View를 결정하고 반환
7. 결과를 처리할 View에 ModelAndView를 전달
8. DispatcherServlet은 View가 만들어낸 결과를 응답

<br>

## Spring MVC (DispatcherServlet)

DispatcherServlet이라고 하는 것은 설정 파일이 필요하다. 이전에는 설정 파일로 applicationContext.xml 라는 파일을 한 개 만들었다. 그런데 DispatcherServlet에는 설정 파일이 두 개 필요하다.

1. <b>Servlet WebApplicationContext</b> - Controllers, ViewResolver, HandlerMapping
2. <b>Root WebApplicationContext</b> - Services, Repositories

이름은 관례적으로 servletContext.xml, rootContext.xml 로 짓는다. servletContext.xml 에는 어떤 것들을 빈으로 관리를 하고 있을까? Controllers, ViewResolver, HandlerMapping을 servletContext에서 빈으로 관리한다. rootContext에서는 Services, Repositories 등을 관리한다.

Web과 직접적으로 관련이 있는 것은 Servlet WebApplicationContext에서 관리를 하고, Web과 직접적인 관련이 없는 것은 Root WebApplicationContext에서 관리를 한다.

즉, Services, Repositories라는 것들은 Web과 직접적인 연관이 있는 것은 아니다. 그냥 Controller에 의해서 로직을 처리하기 위해 호출이 되는 것들이다. 필요하다면 데이터베이스에 접근까지 하는 것이다.

반면 Controllers, ViewResolver, HandlerMapping은 Web과 직접적인 관련이 있다.

이렇게 구분하는 이유는, <u>명확하게 역할을 부여하고 나눔으로써 분업을 할 수 있게 하는 것</u>이다.

<br>

## Spring Web MVC 구성하기

* Dynamic Web PJT 생성 (web.xml 체크)
* maven 프로젝트로 변경
* Spring Web MVC 추가

그래서 Web MVC를 하기 위해서는 'Spring Web MVC'를 만들어야 한다.

<br>

오랜만에 Dynamic Web Project를 만들 것이다. Spring_03_WebMVC 라는 이름으로 Dynamic Web Project를 하나 생성한다.

maven 프로젝트로 변경하고, Spring Web MVC 를 추가해야 한다. 버전은 6.1.3 으로 하겠다.

pom.xml 의 코드는 다음과 같다.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>Spring_03_WebMVC</groupId>
  <artifactId>Spring_03_WebMVC</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <packaging>war</packaging>
  <build>
    <plugins>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.1</version>
        <configuration>
          <release>17</release>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-war-plugin</artifactId>
        <version>3.2.3</version>
      </plugin>
    </plugins>
  </build>
  <dependencies>
	<dependency>
    	<groupId>org.springframework</groupId>
    	<artifactId>spring-webmvc</artifactId>
    	<version>6.1.3</version>
	</dependency>
  </dependencies>
</project>
```

web.xml 도 만들었기 때문에 구조는 다음과 같이 나온다.

<img width="288" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/aac9a433-5305-403e-b683-2bd59b36ae0c">

<br>

이제 DispatcherServlet 을 등록해야 한다. DispatcherServlet은 누가 만들었냐 하면, Spring이 만든 것이다. 우리가 Servlet을 등록하는 방법은 크게 2 가지가 있었는데, 첫 번째 방식은 web.xml에 직접 등록하는 방식이고, 두 번째 방식은 ```@WebServlet``` 어노테이션을 이용하는 방식이다. 그런데 내가 만든 서블릿은 web.xml을 통해서 등록할 수 있었다. ```@WebServlet``` 어노테이션을 이용해서도 내가 만든 서블릿을 등록할 수 있었다. 

내가 만든 것이 아닌, 남(Spring)이 만든 서블릿은 web.xml로 등록할 수 있다. 그런데 어노테이션을 이용해서는 등록할 수 없다. 내가 짠 코드가 아니기 때문에 그 코드에 가서 어노테이션을 붙일 수 없다.

그러니까 DispatcherServlet은 남이 만든 것이고, 그것을 등록하기 위해서는 web.xml 을 통해서 등록을 해야 한다.

web.xml 을 통해 등록한 코드는 다음과 같다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="https://jakarta.ee/xml/ns/jakartaee" xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd" id="WebApp_ID" version="6.0">
  <display-name>Spring_03_WebMVC</display-name>
  
  <servlet>
  	<servlet-name>springDispatcherServlet</servlet-name>
  	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  	<init-param>
  		<param-name>contextConfigLocation</param-name>
  		<param-value>/WEB-INF/servlet-context.xml</param-value>
  	</init-param>
  	<load-on-startup>1</load-on-startup>
  </servlet>
  
  <servlet-mapping>
  	<servlet-name>springDispatcherServlet</servlet-name>
  	<url-pattern>/</url-pattern>
  </servlet-mapping>
  
  
  <listener>
  	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
  
  <context-param>
  	<param-name>contextConfigLocation</param-name>
  	<param-value>/WEB-INF/root-context.xml</param-value>
  </context-param>
  
</web-app>
```

굉장히 복잡하다.

우선 Servlet을 등록하기 위해서는 ```<servlet></servlet>``` 태그를 연다. (이후에는 프로젝트를 이렇게 만들 일이 없다. 한 번 연습해보는 것이다.)

여기에 servlet-name, servlet-class 두 가지를 쓸 수 있다. servlet-name으로는 내 마음대로 "springDispatcherServlet"으로 명명하였다. 그리고 이것이 어디서 왔는지 명시하기 위해 servlet-class를 입력한다. 자동완성이 안 되므로, "org.springframework.web.servlet.DispatcherServlet"을 입력한다. 정확히 어디에 위치해 있는지 보려면, Project Explorer에 Spring_03_WebMVC 에서 Maven Dependencies &#62; spring-webmvc-6.1.3.jar &#62; org.springframework.web.servlet &#62; DispatcherServlet 으로 되어 있다. 이 DispatcherServlet에 마우스 우클릭을 하고 'Copy Qualified Name'을 클릭하면 경로를 따올 수 있다.

<img width="640" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/eea60481-5481-4f1e-b853-7d32f38879b4">

그런데 이렇게 일일이 찾기 복잡하다. 다른 방법으로는, 우선 Java Resources 폴더에 마우스 우클릭 &#62; New &#62; Class 를 클릭한 다음, 임시로 Test 클래스를 생성한다. 그리고 코드에 DispatcherServlet이라고 입력하면 알아서 위에 경로를 import 해준다. 이것을 복사해서 xml에 입력할 수 있다. (이 방식이 제일 편하게 가져올 수 있는 방법이다.)

다시 web.xml 로 돌아와서, 위의 과정까지 했으면 Servlet 등록을 한 것이다. 이제 servlet-mapping을 해야 한다.

servlet-mapping에는 servlet-name과 url-pattern 이 두 개가 들어게가 되어 있다. servlet-name에는 아까 임의로 명명한 "springDispatcherServlet"을 입력하고, url-pattern에는 "/"를 입력한다. 이렇게 "/"를 입력하는 것은 약속 같은 것이다. 이렇게 작성하면 <b>사용자가 보는 모든 요청은 일단 다 DispatcherServlet을 거치게 되어 있다는 뜻</b>이다.

추가적인 설정을 더 해줘야 한다. 우선 ```<load-on-startup>1</load-on-startup>```은 설정 같은 것인데, 이 값이 작을수록 우선순위가 높다고 보면 된다. 값이 1이면 해당 Servlet은 웹 어플리케이션이 시작하는 시점에 즉시 초기화되고 load 된다는 뜻이다.

<br>

그리고 아까 DispatcherServlet을 할 때 두 개의 설정 파일이 필요하다고 했었다.

1. servlet-context.xml 파일
2. root-context.xml 파일

지금은 WEB-INF 밑에 마우스 우클릭 &#62; New &#62; Other 에 가서 xml 파일을 하나 만들 것이고, 이름을 servlet-context.xml 로 생성한다. 그리고 그것을 하나 더 복사해서 root-context.xml 파일을 생성한다.

원래는, 옛날 방식을 보면 이 두 개가 경로가 살짝 다르다. 그런데 지금 연습을 하기 위해서 똑같은 경로에다가 두 개를 만든 것이다. root-context.xml 과 servlet-context.xml 두 개 모두 설정 파일로 이전에 사용했던 Spring_02_AOP_2_XML 프로젝트의 resources/applicationContext.xml 을 복사해서 가져온다. aop와 관련한 것들은 필요하지 않으니 다 지운다.

web.xml로 돌아와서 이제 init-param에 param-name은 "contextConfigLocation" 으로 한다. 그리고 param-value로는 "/WEB-INF/servlet-context.xml"로 입력한다. 

그리고 load-on-startup에는 1을 입력한다. 

root-context.xml 에 설정을 넣을 것이다. listener이라는 것이 있고, context-param 이라고 하는 것 두 개를 추가를 해줘야 한다. listener은 무언가 이벤트가 발생했을 때 듣고 있는 청취자이다. 그러니까 위 코드에서 listener-class에 써 있는 것은 Context가 로드가 되었을 때 context-param에다가 이러한 설정을 넣어서 보내겠다는 것이다.

listener에는 우선 listener-class를 넣어서 ContextLoaderListener의 전체 경로를 넣을 것이다. 마찬가지로 임시 Test 파일에 가서 ContextLoaderListener를 입력하고 import 하면 full path를 쉽게 알 수 있다.

그리고 밑에 context-param에는 context의 설정을 넣을 것이다. context에 설정을 넣을 것인데, param-name은 아까와 동일하게 "ContextConfigLocation"으로, param-value는 "/WEB-INF/root-context.xml"로 입력한다. ContextLoaderListener이 로드 될 때 이러한 설정과, DispatcherServlet이 실행할 때의 설정들을 이용해서 완성을 하는 것이다. 이제 web.xml은 다 작성이 되었다.

<br>

이제 servlet-context.xml 를 조금 더 완성을 해야 한다.

DispatcherServlet은 어떤 요청이 들어오면 HandlerMapping을 통해서 갔다오고, 갔다온 것을 토대로 컨트롤러에 갔다가, 그것을 토대로 ViewResolver에 간다.

이러한 웹과 관련된 설정들은 servlet-context.xml 에 등록을 한다.

HandlerMapping이라는 객체는 DefaultAnnotationHandlerMapping을 사용하기 때문에 별도의 등록 없이 사용이 가능하다. (Spring 3.0 부터) 그러면 우리는 ViewResolver만 등록해주면 된다. (물론 컨트롤러도 등록을 해야 하는데, 컨트롤러는 그냥 컴포넌트 스캔을 쓸 것이고, ```@Controller``` 어노테이션을 집어넣으면 된다.)

그런데 InternalResourceViewResolver은 내가 직접 만드는 것이 아니라 등록을 해야 한다.

<br>

servlet-context.xml 파일로 와서, 핸들러 매핑은 기본으로 사용하는 게 있어서 내가 굳이 등록하지 않아도 OK 이다. 그런데 ViewResolver은 기본으로 사용하는 게 없기 때문에 내가 직접 등록을 해야 한다.

InternalResourceViewResolver를 가져오기 위해 또 임의의 Test 클래스에서 입력하고 full path를 복사한다. 그리고 servlet-context.xml 에서 ```<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">```로 입력한다.

그리고 앞에 "/WEB-INF/"인 접두어를 붙이고 뒤에는 ".jsp"인 접미어를 붙인다고 했었다.

빈으로 등록할 때 property를 통해서 ```<property name="prefix" value="/WEB-INF/view/"></property>```로 입력하고, ```<property name="suffix" value=".jsp"><property>```의 방식으로 입력한다. ".jsp" 확장자를 무조건 붙일 것이라는 것이다.

servlet-context.xml 의 코드는 다음과 같다.

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

		<!-- Web과 관련된 설정 -->
		
		<!-- 핸들러매핑은 기본으로 사용하는게 있어서 내가 굳이 등록하지 않았도 OK -->
		
		<!-- 뷰리졸버는 내가 직접 등록을 해주어야 해 -->
		<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
			<property name="prefix" value="/WEB-INF/view/"></property>
			<property name="suffix" value=".jsp"></property>
		</bean>
		
</beans>
```

property라는 것이 있다면 이것은 즉 setter이 있다는 것이다. "org.springframework.web.servlet.view.InternalResourceViewResolver"를 클릭하고 들어가보면 내부적으로 setPrefix, setSuffix 라는 메서드가 있다. 그렇기 때문에 property를 통해서 접근할 수 있는 것이다. (다 이미 학습한 내용이다.)

<br>

여기까지 했으면 이제 "hello"를 찍어볼 준비가 다 되었다.

코드로 넘어와서, WEB-INF 폴더에 마우스 우클릭을 하고 "view"라는 폴더를 하나 만든다. ("/WEB-INF/view/"로 다 접근을 하기로 했기 때문)

여기에 index.jsp 라는 파일을 하나 만든다. index.jsp 의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Spring MVC</title>
</head>
<body>
	<h2>Hello Spring MVC</h2>
</body>
</html>
```

일단 여기까지 와야 하니까 컨트롤러를 하나 만들어줘야 한다. Package는 "com.ssafy.mvc.controller"로 한다. 그리고 Name은 "MyController"로 한다.

기존에는 여기에 무언가 붙였겠지만, 스프링은 POJO 스타일을 추구하니 여기에 무엇을 안 붙인다. 그냥 위에 ```@Controller``` 어노테이션만 붙인다. MyController의 코드는 다음과 같다.

```java
package com.ssafy.mvc.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class MyController {
	@RequestMapping("/")
	public String index() {
		return "index";
	}
	
}
```

여기서 컨트롤러를 등록을 해줘야 쓸 수 있다. 다시 servlet-context.xml 로 돌아와서 ```<context:component-scan base-package="com.ssafy.mvc.controller"></context:component-scan>```를 붙여준다. 그러면 최종적인 servlet-context.xml 의 코드는 다음과 같다.

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

		<!-- Web과 관련된 설정 -->
		
		<!-- 핸들러매핑은 기본으로 사용하는게 있어서 내가 굳이 등록하지 않았도 OK -->
		
		<!-- 뷰리졸버는 내가 직접 등록을 해주어야 해 -->
		<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
			<property name="prefix" value="/WEB-INF/view/"></property>
			<property name="suffix" value=".jsp"></property>
		</bean>
		
		<context:component-scan base-package="com.ssafy.mvc.controller"></context:component-scan>
		
</beans>
```

<br>

이제 드디어 실행이다. Project Explorer에 있는 Spring_03_WebMVC에 마우스 우클릭을 하고, Run on Server를 클릭한 다음, Tomcat을 결정하고 Next를 클릭하고 Finish를 누른다. 그러면 context-root 까지 실행해서 다음과 같은 index 페이지가 뜬다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4fcb638c-236a-4e37-afc7-db8a49c18ae0" width="480px">

<br>

결국 지금까지 한 것은 전부 다 설정이다. 설정 파일 만들고, DispatcherServlet 등록하고, ViewResolver 등록하고, Component 등록하고, Controller 등록하는 등... 설정만 한 것이다. 그래서 설정이 반이다.

스프링이 진입장벽이 좀 있다고 하는 것이, 이러한 설정들이 엄청 많기 때문이다.

그래서 순수하게 스프링을 통해서 기술만 좀 익히겠다고 한다면 바로 Spring Boot 로 가면 되기는 한다. 하지만 여기까지 왔는데 스프링 부트만 배우고 가는 것은 맞지 않는다. 그래서 이러한 스텝을 하나씩 밟아보면서 하는 것이다.

<br>

## Spring Web MVC 실습

RequestMapping이라는 것을 아까 살펴봤었다. RequestMapping의 특징은 다음과 같다.

* URL을 클래스 또는 특정 핸들러(메서드)에 매핑
* 메서드 Annotation은 요청 방식(GET, POST) 등으로 범위를 좁혀 준다.

<br>

다시 코드로 넘어가보자. view 폴더 아래에 home.jsp 파일을 하나 생성한다. home.jsp 의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Spring MVC</title>
</head>
<body>
	<h2>${msg}</h2>
</body>
</html>
```

EL 표현식으로 ```${msg}```를 입력하였다. jsp가 접근할 수 있는 영역에 "msg"라고 하는 것이 있으면 가져오는 것이다.

이제 MyController에 가서, 다음과 같이 코드를 추가한다.

```java
package com.ssafy.mvc.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class MyController {
	@RequestMapping("/")
	public String index() {
		return "index";
	}
	
	@RequestMapping(value="home", method = RequestMethod.GET)
	public ModelAndView homeHandle1() {
		ModelAndView mav = new ModelAndView();
		//데이터를 실어서 보내보자. (키/벨류)
		mav.addObject("msg", "Welcome to Spring (GET)");
		
		//view 이름을 결정해야해 //포워딩 한것과 같음
		mav.setViewName("home");
		return mav;
	}
	@RequestMapping(value="home", method = RequestMethod.POST)
	public ModelAndView homeHandle2() {
		ModelAndView mav = new ModelAndView();
		//데이터를 실어서 보내보자. (키/벨류)
		mav.addObject("msg", "Welcome to Spring (POST)");
		
		//view 이름을 결정해야해 //포워딩 한것과 같음
		mav.setViewName("home");
		return mav;
	}
		
}
```

위 코드에서 ModelAndView 객체를 반환하기로 했다. 그래서 ```ModelAndView mav = new ModelAndView();```의 방식으로 입력하였다. ModelAndView는 바구니와 이름을 저장할 수 있는 객체이다. 여기에 키-밸류 방식으로 데이터를 실어서 보내보겠다.

```mav.addObject()```를 하여 속성 이름은 "msg"로, 객체로는 "Welcome to Spring (GET)"을 입력한다. (GET 방식으로 보냈다는 것을 쓰겠다.)

이렇게 ModelAndView를 이용해서 데이터를 실었고, 이제는 view의 이름을 결정해야 한다. 그래서 ```mav.setViewName("home");```로 이름을 결정한다. 이렇게 넘기면 포워딩(forwarding)을 한 것과 똑같다. 그리고 mav를 리턴한다.

내가 지금 ModelAndView라는 바구니를 준비했고, 메세지를 담았으며 "home"이라는 곳으로 가면 되고, 그리고 mav를 반환한다고 해 놨다. 그러면 ViewResolver가 "home"이란 이름을 가지고 앞에는 "/WEB-INF/"를 붙이고 뒤에는 ".jsp"를 붙여서 사용자에게 보내주는 것이다. 여기에 있는 homeHandle1() 위에 ```@RequestMapping("home")```이라고 어노테이션을 보내면 요청이 이렇게 올 것이다.

다시 브라우저에 가서 뒤에 URL 뒤에 "home"을 붙이면 다음과 같은 화면이 나타난다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f48f0c10-4f36-49cb-a33a-5c3b98b4fbbd" width="480px">

<br>

그런데 이렇게 RequestMapping만 쓰면 이것이 GET 방식이든 POST 방식이든 전부 다 처리를 해버린다. 그래서 조금 더 명확하게 하기 위해서 ```@RequestMapping(value="home", method = RequestMethod.GET)```로 명시를 할 것이다.

그리고 이 homeHandle1()을 복사 붙여넣기 하여 homeHandle2()를 만들고, 이번에는 POST 방식으로 설정을 해둔다.

주소에 직접 써서 보내는 것은 GET 방식이다. POST 방식은 form 태그를 통해서 action="home", 그리고 method="POST"로 하면 된다.

index.jsp의 코드에 다음과 같이 form 태그를 추가한다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Spring MVC</title>
</head>
<body>
	<h2>Hello Spring MVC</h2>
	
	<form action="home" method="POST">
		<button>등록</button>
	</form>
</body>
</html>
```

그리고 Run on Server을 한 다음, "등록" 버튼을 클릭하면 다음과 같이 화면에 출력된다.

<image src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e446d923-0cb6-4ab5-8059-2e3830a3bef5" width="480px">

그러면 POST 방식으로 된다. (POST로 보낼 수 있는 방법은 지금으로써는 이게 유일하다.)

<br>

### Controller Parameter

<table>
    <tr>
        <th>파라미터 타입</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>HttpServletRequest<br>HttpServletResponse<br>HttpSession</td>
        <td>Servlet API를 사용할 수 있다</td>
    </tr>
    <tr>
        <td>Locale</td>
        <td>요청 클라이언트의 Locale 정보를 포함</td>
    </tr>
    <tr>
        <td>InputStream, Reader<br>OutputStream, Writer</td>
        <td>요청으로부터 직접 데이터를 읽어오거나, 응답을 직접 생성하기 위해서 사용</td>
    </tr>
    <tr>
        <td>Map, Model, ModelMap</td>
        <td>View 데이터를 전달하기 위해서 사용</td>
    </tr>
    <tr>
        <td>RedirectAttributes</td>
        <td>리디렉션(쿼리 문자열에 추가) 시 사용할 속성 지정</td>
    </tr>
    <tr>
        <td>Errors, BindingResult</td>
        <td>에러와 데이터 바인딩 결과에 접근하기 위해서 사용</td>
    </tr>
    <tr>
        <td>@PathVariable</td>
        <td>URI 템플릿 변수에 대한 액세스</td>
    </tr>
    <tr>
        <td>@RequestParam</td>
        <td>multipart 파일을 포함하여 요청 파라미터에 액세스</td>
    </tr>
    <tr>
        <td>@RequestHeader</td>
        <td>요청 헤더에 액세스</td>
    </tr>
    <tr>
        <td>@CookieValue</td>
        <td>쿠키에 대한 액세스</td>
    </tr>
    <tr>
        <td>@RequestAttribute<br>@SessionAttribute</td>
        <td>모든 세션 요청에 대한 액세스<br>요청 속성에 액세스</td>
    </tr>
    <tr>
        <td>@ModelAttribute</td>
        <td>모델의 속성에 액세스</td>
    </tr>
</table>

Controller Parameter에는 위와 같이 파라미터 타입들을 쓰기만 하면 알아서 넣어 준다. 내가 Request를 쓰고 싶으면 Controller Parameter 안에다가 HttpServletRequest라고 하는 파라미터를 작성만 하면 "아 너 이거 필요하구나" 하고 알아서 의존성 주입해서 넘겨주는 것이다.

또 그 안에 @PathVariable, @RequestParam, @RequestHeader 등등이 있다.

<br>

### Controller Return Type

<table>
    <tr>
        <th>파라미터 타입</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>@ResponseBody</td>
        <td>HttpMessageConverter 구현을 통해 변환되어 응답한다.</td>
    </tr>
    <tr>
        <td>HttpHeaders</td>
        <td>헤더가 있고 body가 없는 response를 반환</td>
    </tr>
    <tr>
        <td>String</td>
        <td>뷰 이름 반환 (ViewResolver 선언과 함께 사용)</td>
    </tr>
    <tr>
        <td>View</td>
        <td>렌더링 하는데 사용할 View 인스턴스</td>
    </tr>
    <tr>
        <td>Map, Model</td>
        <td>명시적으로 모델을 작성하지 않는 경우 사용</td>
    </tr>
    <tr>
        <td>ModelAndView</td>
        <td>사용할 view와 속성을 포함하는 객체</td>
    </tr>
    <tr>
        <td>void</td>
        <td>method에 ServletResponse, HttpServletResponse 인자가 있는 경우, 모든 요청이 처리된 것으로 간주, 그렇지 않으면 요청 URI를 view name으로 처리</td>
    </tr>
</table>

<br>

이제 본격적으로 실습을 진행해보자.

com.ssafy.mvc.controller 패키지 안에 ControllerParameter 클래스를 생성한다. ControllerParameter 클래스의 코드는 다음과 같다.

```java
package com.ssafy.mvc.controller;

import java.util.Map;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

import jakarta.servlet.http.HttpServletRequest;

@Controller
public class ControllerParameter {
	
	@GetMapping("/test1") // 요청 경로
	public String test1() { // 메서드명은 지금은 중요치 않다.
		
		// 반환 타입이 String이라는 뜻은 해당 값이 ViewName이다.
		return "test1"; // WEB-INF/view/test1.jsp
	}
	
	
	// 데이터를 실어서 보내고 싶은데, ModelAndView로 하긴 싫다.
	@GetMapping("/test2-1")
	public String test2_1(Model model) {
		model.addAttribute("msg", "데이터야");
		return "test2";
	}
	
	// 데이터를 실어서 보내고 싶은데, ModelAndView로 하긴 싫다.
	@GetMapping("/test2-2")
	public String test2_2(Map<String, Object> model) {
		model.put("msg", "데이터야2");
		return "test2";
	}
	
	
//	// 파라미터로 값을 가져오고 싶다.
//	@GetMapping("/test3")
//	public String test3(Model model, HttpServletRequest request) {
//		String id = request.getParameter("id");
//		
//		model.addAttribute("id", id);
//		return "test3";
//	}
	// 파라미터로 값을 가져오고 싶다.
	@GetMapping("/test3")
	public String test3(Model model, @RequestParam("myid") String id, @RequestParam(value="pw", defaultValue = "1234") String pw) {
		model.addAttribute("id", id);
		model.addAttribute("pw", pw);
		return "test3";
	}
		
}
```

우선 위에 ```@Controller```를 입력하여 컨트롤러로 등록을 한다. 이전에 했던 MyController 클래스를 생각해봤을 때, GET 방식으로 가려면 ```@RequestMapping(value="home", method = RequestMethod.GET)```, POST 방식으로 가려면 ```@RequestMapping(value="home", method = RequestMethod.POST)``` 의 방식으로 해야 했다. 이렇게 하면 생각보다 불편하다.

그래서 위와 같이 ```@GetMapping("/test1")```와 같이 입력한다. 그러면 "/test1" 이라는 GET 요청은 전부 여기로 들어오는 것이다. (굉장히 편해졌다.) 그리고 "test1"을 리턴한다.

WEB-INF/view 안에 test1.jsp를 생성한다. 그리고 test1.jsp의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Spring MVC</title>
</head>
<body>
	<h2>ControllerParameter Test1</h2>
</body>
</html>
```

이제 실행을 시킨 다음, 주소를 "http://localhost:8080/Spring_03_WebMVC/test1"로 해서 들어가면 다음과 같이 출력된다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/3c319b42-79fe-4b5c-869c-786242bebd6a" width="480px" />

<br>

test1에서 지금 반환 타입은 String이다. 그리고 ```return "test1";```라고 해뒀다. 이때 반환 타입이 String이라는 뜻은 해당 값이 ViewName이라는 것이다. 그러니까 내가 String을 반환하면 ViewName으로 알아서 지정이 되는 것이다. 내부적으로 ViewResolver이 잡아다가 "/WEB-INF/view/test1.jsp"로 바꿔서 실행을 해버리는 것이다.

그리고 어노테이션 ```@GetMapping("/test1")```에 있는 "/test1"은 요청 경로이다. (메서드명은 지금은 중요하지 않다.)

<br>

이번에는 ```@GetMapping("/test2_1")```을 입력한다. 메서드명도 동일하게 "test2_1"로 한다. 그리고 "test2"를 리턴한다. 지금 이 상황은 위와 동일한 것이다.

test2.jsp를 하나 생성한다. test2.jsp의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Spring MVC</title>
</head>
<body>
	<h2>ControllerParameter Test2</h2>
	<div>
		${msg}
	</div>
</body>
</html>
```

그런데 데이터를 실어서 보내고 싶은데 ModelAndView를 반환하기는 싫은 상황이다. 그러면 바구니가 필요한 상황이다.

이전에 봤던 Controller Parameter에서 파라미터 타입들을 쭉 보면, Map, Model, ModelMap이 바구니인 것을 알 수 있다. View 데이터를 전달하기 위해서 사용하는 것들이다.

그래서 ```public String test2_1(Model model) {```로 하여 바구니를 가져온 것이다. 그냥 알아서 객체를 만들어서 넣어준다. (스프링 컨테이너가 이것을 다 해준다.)

```model.addAttribute("msg", "데이터야");```를 입력한다. "msg"라는 이름으로 데이터를 실어서 보내는 것이다.

이제 다시 Run on Server을 하고, 웹 브라우저에서 "http://localhost:8080/Spring_03_WebMVC/test2-1"로 들어가면 다음과 같이 출력된다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e4c4fc29-21df-4360-8587-9168e91d8378" width="480px" />

<br>

이렇게 Model을 쓸 수도 있었고, Map을 쓸 수도 있었다. test2_2를 만들고 다음과 같이 입력한다. ```public String test2_2(Map<String, Object> model) {``` Map은 이 경우에는 String, Object의 형태로 받을 것이다.

그리고 다시 Run on Server을 하고, ""를 웹 브라우저에 입력하고 들어가면 다음과 같이 화면에 출력된다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/19b0328e-1bba-403e-89f4-8366e64824ee" width="480px" />

결국에는 Model, Map 둘 다 쓸 수 있다. (둘이 같아 보이지만, 각각의 용도가 있기는 하다.)

<br>

이번에는 파라미터로 값을 가져오고 싶다. 이번에는 ```@GetMapping("/test3")```이다. 메서드명은 test3 로 한다. 파라미터 안에 ```Model model```을 넣는다.

test3.jsp 파일을 생성한다. 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Spring MVC</title>
</head>
<body>
	<h2>ControllerParameter Test3</h2>
	<div>
		id : ${id} <br>
		pw : ${pw} <br>
	</div>
</body>
</html>
```

이번에는 id와 pw를 넘겨받는다. 이렇게 했으면 id, pw가 있어서 찾아서 던지겠다는 것이다.

<br>

다시 ControllerParameter로 와서, id와 pw를 집어넣어주고 출력을 해볼 것이다. 웹 브라우저에 "http://localhost:8080/Spring_03_WebMVC/test3?id=ssafy&pw=1234" 와 같이 입력하여 요청을 보낼 것이다.

그래서 ```HttpServletRequest request```를 통해서, ```String id = request.getParameter("id");```를 통해서 id를 받아오고 이것을 ```model.addAttribute("id", id);```로 하여 model에 담는다.