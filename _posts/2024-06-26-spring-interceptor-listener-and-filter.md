---
layout: post
title: Interceptor - Listener & Filter
categories: Spring-SpringBoot
date: 2024-06-26 23:48:00 +0900
---
Filter와 Interceptor의 내용에 대해 알아보겠다. 그 중에 먼저 Listener와 Filter에 대해 알아보겠다.

Listener에 대해서는 MVC를 공부할 때 알아본 적이 있다. root-context.xml에서 무언가 집어넣으려 할 때 리스너를 통해 등록한 적이 있다. Filter는 뭔지 모르겠지만 일상 생활에서도 너무 많이 사용하는 용어이다 보니, 무언가를 '거르는' 용도로 쓰이는 것을 예상할 수 있다. input이 filter를 통해서 바뀔 수 있다는 것을 생각해볼 수 있겠다.

## Listener

우선 Listener에 대해 알아보자. Listener의 특징들은 다음과 같다.

* 프로그래밍에서 Listener란 특정 이벤트가 발생하기를 기다리다가 실행되는 객체
* 이벤트란 특정한 사건 발생. Ex&#41; 버튼클릭, 키보드입력, 컨테이너 빌드 완료, 웹어플리케이션 시작, HTTP요청수신 등...
* 서블릿 컨테이너에서 발생하는 이벤트 감지
* web.xml 파일에 &#60;listener&#62; 태그를 이용하여 사용 가능
* 리스너가 여러 개일 경우 보통 선언된 순서대로 실행되지만 아닌 경우가 있으므로 각각의 리스너는 독립적으로 동작할 수 있도록 설계하는 것이 좋음

Listener는 "귀를 연다"로 생각하면 된다. 자바스크립트에서 버튼 클릭을 했을 때 eventListener을 쓸 수 있는데, 마찬가지로 자바에서도 이런 리스너를 쓸 수 있다. 이때 '특정 이벤트'란 버튼을 클릭했다든지, 키보드를 다 쳤다든지.. 가 될 수 있겠다.

컨테이너 빌드가 완료되었다는 것은, 무언가 실행을 했을 때 서블릿 컨테이너라든지, IoC 컨테이너라든지가 완성이 되었다면 이것을 하나의 이벤트라고 볼 수 있는 것이다.

리스너1, 리스너2를 선언했을 때, 두 번째 리스너는 첫 번째 리스너가 처리된 이후에 하는 느낌으로 선언을 할 수 있다. 하지만 아닌 경우도 있다. 어떤 환경에 따라서 리스너2가 먼저 실행될 수도 있는 것이다. 그러므로 각각의 리스너는 독립적으로 동작할 수 있게 설계하는 것이 좋다.

<br>

## Listener 사용 (Annotation)

Listener를 사용하는 방법은 Annotation을 사용하는 방법과, web.xml에 등록하는 방법의 두 가지 방법이 있다.

그러면 바로 코드를 보면서 얘기해보겠다. (지금 하는 것은 스프링과 관련이 따로 없다.)

STS에서 "Spring_04_Listener_Filter"라는 이름으로 Dynamic Web Project를 생성한다. Next를 여러 번 클릭하고 "Generate web.xml"에 체크하고 Finish를 클릭한다.

이제 Project Explorer 쪽에서 src/main/java에 우클릭을 하고 Create Listener를 클릭한다. package이름은 "com.ssafy.mvc.listener"로 하고, 클래스 이름은 "MyListener1"으로 한다. 그리고 Next를 클릭하면 각종 인터페이스를 선택하는 화면이 나타난다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/91fff213-eb50-4878-bddb-f139971dc1c1" width="450px" />

Servlet context events 와 관련된 것을 처리하려면 위 두개를 체크하면 되고, HTTP session events와 관련된 것을 처리하려면 그와 관련된 것들을 체크하면 되며, Servlet request events와 관련된 것을 처리하려면 또 그와 관련된 것들을 체크하면 된다. (직접 작성해도 된다.) 이 경우에는 맨 위의 Lifecycle을 하나 체크하고 Next를 클릭한다. 그리고 "Inherited abstract methods"에 체크된 것을 유지한 채 Finish를 클릭한다.

그러면 추상 메서드들이 작성되어 있는 MyListener1 이 생성된다.

<br>

지금 기본은 web.xml에 다음과 같이 입력되어 동작하고 있다. 

```xml
<listener>
    <listener-class>com.ssafy.mvc.listener.MyListener1</listener-class>
</listener>
```

web.xml이 기본이었다가 그 이후에는 Annotation 방식이 기본이 되었다. 그러다가 다시 web.xml이 기본으로 바뀌었다. 이제 따로 등록하지 않아도 생성만 하면 알아서 web.xml에 등록을 해버린다.

그런데 이번에는 Annotation 방식을 사용해보기로 했으니, 일단 지우겠다.

<br>

결국 MyListener1 도 클래스이다. 클래스에 ```implements ServletContextListener``` 를 한 것이다. 이것은 스프링에서 썼던 POJO(Plain Old Java Object) 방식이 아니다. 일반 클래스에 ~ 한 기능을 구현해야 한다는 것을 집어넣은 것이니 POJO 방식은 아니다.

MyListener1 클래스의 코드는 다음과 같다.

```java
package com.ssafy.mvc.listener;

import jakarta.servlet.ServletContextEvent;
import jakarta.servlet.ServletContextListener;
import jakarta.servlet.annotation.WebListener;

@WebListener
public class MyListener1 implements ServletContextListener {

    public void contextInitialized(ServletContextEvent sce)  { 
    	System.out.println("웹어플리케이션 시작될때 호출1");
    }

    public void contextDestroyed(ServletContextEvent sce)  { 
    	System.out.println("웹어플리케이션 종료될때 호출1");
    }
	
}
```

```@WebListener```라는 어노테이션을 이용하면 동작을 한다. ```@WebServlet``` 어노테이션을 쓸 때에는 매핑된 경로를 옆에 작성했었다. 그런데 ```@WebListener``` 어노테이션에서는 그런 것을 따로 쓰라는 말이 없다. 왜냐하면 이것 자체대로 ServletContext가 처리되었을 때 그것에 대한 이벤트를 청취하겠다는 것이 있으니까 Listener를 만들어서 등록만 하면 이러한 이벤트가 발생했을 때 알아서 동작을 해버리는 것이다. 이미 매핑되어 있는 것을 정의를 할 것이니 만들어져 있는 것이다. 기본 생성자는 필요 없으니까 지워버리겠다.

contextInitialized는 딱 봐도 초기화와 관련된 것이다. 그래서 "웹어플리케이션 시작될 때 호출1"을 출력하겠다. contextDestroyed에서는 "웹어플리케이션 종료될때 호출1"을 출력해보겠다.

이제 실행을 해보겠다. 화면을 볼 필요는 없으니, Window &#62; Web Browser &#62; 0 Internal Web Browser 로 변경을 해두겠다. (콘솔창에 조금 더 집중을 해볼 것이니까) Run on Server을 한다. 그리고 Console 을 클릭한다.

그러면 콘솔창에 "웹어플리케이션 시작될때 호출1"이라는 것이 나타난다. 만약 내용을 약간이라도 수정하고 저장하면 리-로딩 되어 "웹어플리케이션 종료될때 호출1"이 출력되고 다시 "웹어플리케이션 시작될때 호출1"이 출력된다.

<br>

이번에는 MyListener2 클래스를 생성하겠다. web.xml에 등록하는 방식을 사용해볼 것이다. Create Listener로 생성하면 자동으로 web.xml에 다음과 같은 코드가 생성된다.

```xml
<listener>
    <listener-class>com.ssafy.mvc.listener.MyListener2</listener-class>
</listener>
```

MyListener2의 코드는 일단 다음과 같이 작성한다.

```java
package com.ssafy.mvc.listener;

import jakarta.servlet.ServletContext;
import jakarta.servlet.ServletContextEvent;
import jakarta.servlet.ServletContextListener;

public class MyListener2 implements ServletContextListener {

	public void contextInitialized(ServletContextEvent sce) {
		System.out.println("웹어플리케이션 시작될때 호출2 ");

	}

	public void contextDestroyed(ServletContextEvent sce) {
		System.out.println("웹어플리케이션 종료될때 호출2");
		
	}

}
```

그리고 다시 web.xml로 돌아가보자. web.xml에 추가적인 설정을 할 수 있다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="https://jakarta.ee/xml/ns/jakartaee" xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd" id="WebApp_ID" version="6.0">
  <display-name>Spring_04_Listener_Filter</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.jsp</welcome-file>
    <welcome-file>default.htm</welcome-file>
  </welcome-file-list>
  <listener>
    <listener-class>com.ssafy.mvc.listener.MyListener2</listener-class>
  </listener>
  <context-param>
    <param-name>welcome</param-name>
    <param-value>Hello SSAFY Listener</param-value>
  </context-param>
</web-app>
```

여기에 내가 context-param을 설정할 수 있다. 이렇게 하면 web.xml 자체가 context가 될 때 context-param으로 "welcome"이라는 이름으로 param-value를 집어넣었다.

그러면 다시 MyListener2로 돌아와서 이렇게 설정한 것을 꺼내고 싶을 수 있다. ServletContext에 "welcome"이라는 이름으로 무언가를 하나 넣어놓은 상황이다. 

그러면 ServletContext라는 설정 파일을 가져와야 한다. 이름은 context로 하겠다. 수정한 MyListener2는 다음과 같다.

```java
package com.ssafy.mvc.listener;

import jakarta.servlet.ServletContext;
import jakarta.servlet.ServletContextEvent;
import jakarta.servlet.ServletContextListener;

public class MyListener2 implements ServletContextListener {

	public void contextInitialized(ServletContextEvent sce) {
		System.out.println("웹어플리케이션 시작될때 호출2");		

		ServletContext context = sce.getServletContext();
		System.out.println("welcome : " + context.getInitParameter("welcome"));
	}

	public void contextDestroyed(ServletContextEvent sce) {
		System.out.println("웹어플리케이션 종료될때 호출2");
		
	}

}
```

ServletContextEvent 객체가 넘어오는데, 이 객체는 ServletContext에 걸려있는 이벤트이니까 getServletContext() 로 설정들을 가져올 수 있다. 그리고 System.out.println으로 출력을 한다. 여러 메서드들 중에서 지금은 ```getInitParameter()```을 가져와보겠다. 그리고 괄호 안에 "welcome"을 쓴다. web.xml에 "welcome"으로 context-param을 설정해뒀기 때문이다.

