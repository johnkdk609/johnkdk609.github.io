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

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/9f346c04-918c-42a3-bb23-27534dfedf6a" width="800px" />

<br>

이번에는 web.xml에 다음과 같은 코드를 추가한다.

```xml
<listener>
    <listener-class>com.ssafy.mvc.listener.MyListener1</listener-class>
</listener>
```

그리고 MyListener1 클래스의 어노테이션 ```@WebListener```을 주석 처리한다. 그리고 다시 Run on Server을 하면 다음과 같이 출력된다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7ad4aeff-6999-4117-9c2e-fa3cc2f85b8f" width="800px" />

<br>

이번에는 1번이 호출되고, 2번이 호출된다. 등록이 된 순서에 따라 동작을 하는데, 혼용을 했을 때에는 살짝 꼬일 수 있다. 그래서 한 가지 방식으로 통일하는 것이 좋다. 그것과 별개로, 여러 개를 등록할 때 편하게 사용하기 위해 어떠한 방식으로 등록하든 Listener1, Listener2 등이 독립적으로 존재해야 한다.

<br>

이제 Listener는 끝났다. root-context가 Listener를 통해서 설정 파일들을 넣어줬고, 로드 하면서 처리 되었다는 것을 Listener로 이해하면 되겠다.

<br>

## Filter

이제 Filter에 대해 알아보자. Filter의 설명은 다음과 같다.

* 요청과 응답 데이터를 필터링하여 제어, 변경하는 역할
* 사용자의 요청이 Servlet에 전달되어지기 전에 Filter를 거침
* Servlet으로부터 응답이 사용자에게 전달되어지기 전에 Filter를 거침
* FilterChain을 통해 연쇄적으로 동작 가능

내 요청이 서버로 가는 도중에 Filter들을 거치기도 하지만, 응답이 내게 오기 전에 Filter들을 거치기도 한다. Filter라는 것을 통해 요청과 응답에 추가적인 처리를 할 수 있는 것이다.

그리고 Filter들을 여러 개 나열하는 것을 FilterChain이라고 한다. "OK, 다음 필터로", "OK 다음 필터로.."를 반복적으로 할 수 있는 것이다. 

이 Filter은 web.xml에 등록해서 사용할 것이다. 만약 어노테이션 방식으로 하고 순서를 조절하려면, ```@Order```라는 어노테이션을 추가적으로 사용해야 한다.

Filter은 독립적으로 존재할 필요 없다. 이어서 해야 하니까.

<br>

이제 실습을 해보겠다. Spring_04_Listener_Filter 프로젝트에서 src/main/java에 마우스 우클릭을 하고 Create Filter을 클릭한다. 패키지는 com.ssafy.mvc.filter로 하고, 이름은 MyFilter로 한 다음 Next를 클릭한다. 그러면 아까 Listener와는 달리 Initialization parameters, Filter mappings 가 보인다. '경로'가 들어가는 것이다.

'경로'가 들어간다는 것은 무엇일까? "내가 어떤 요청이 들어왔을 때 그 요청에다만 Filter를 걸 수 있다"는 것이다. 내가 특정한 요청에 따라 필터를 걸고 안 걸고를 URL 패턴을 통해서 쓸 수 있다. 이제 Next를 클릭하고, Finish를 클릭한다.

기본 방식은 web.xml이라고 했으니, web.xml에 들어가보면 다음과 같이 코드가 추가되어 있을 것이다.

```xml
<filter>
    <display-name>MyFilter</display-name>
    <filter-name>MyFilter</filter-name>
    <filter-class>com.ssafy.mvc.filter.MyFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>MyFilter</filter-name>
    <url-pattern>/MyFilter</url-pattern>
</filter-mapping>
```

딱 봐도 Servlet 등록하는 것과 똑같이 생겼다. 그러면 여기에서 "/MyFilter"라고 하는 경로로 왔을 때가 아니라, 모든 경로로 처리하기 위해서 다음과 같이 수정한다.

```xml
<filter>
    <display-name>MyFilter</display-name>
    <filter-name>MyFilter</filter-name>
    <filter-class>com.ssafy.mvc.filter.MyFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>MyFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

DispatcherServlet에서 모든 요청을 처리할 때에는 그냥 "/"만 했는데, Filter에서는 "/*"로 입력하는 것은 그냥 약속이다.

<br>

### Filter 사용

Filter을 사용할 때에는 필터 초기화, 필터 종료, 그리고 doFilter 등이 있다.

Filter가 초기화될 때 init-param 태그에 param-name이 "encoding", param-value로 "utf-8"이 들어간다. 이 말은 web.xml에서 Filter를 등록하는데 init-param을 할 수 있다는 것이다. init-param을 했는데 param-name으로 "encoding"을 집어넣고, param-value로 "UTF-8"을 쓸 수 있다는 것이다. 

원래는 UTF-8이 아니었는데 Filter를 통해서, 모든 것을 다 바꾸기 위해서 "UTF-8"이라는 필터를 살짝 중간에 끼워 넣고 통과되는 모든 인코딩 방식을 "UTF-8"로 바꾸었다. 그런데 이제는 UTF-8이 기본 방식이 되었다. 그래서 이렇게 UTF-8이라는 필터를 안 걸어도 찍어보면 UTF-8이라고 뜰 것이다. 그러니까 눈으로 좀 확인을 하기 위해 임시로 UTF-16을 필터로 넣어보겠다.

수정한 web.xml의 코드는 다음과 같다.

```xml
<filter>
    <display-name>MyFilter</display-name>
    <filter-name>MyFilter</filter-name>
    <filter-class>com.ssafy.mvc.filter.MyFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-16</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>MyFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

아무튼 기본은 UTF-8이고, 설정하지 않으면 자동으로 UTF-8이 된다.

<br>

다시 MyFilter 클래스를 보자. MyFilter 클래스의 코드는 다음과 같다.

```java
package com.ssafy.mvc.filter;

import java.io.IOException;

import jakarta.servlet.Filter;
import jakarta.servlet.FilterChain;
import jakarta.servlet.FilterConfig;
import jakarta.servlet.ServletException;
import jakarta.servlet.ServletRequest;
import jakarta.servlet.ServletResponse;
import jakarta.servlet.http.HttpFilter;

public class MyFilter extends HttpFilter implements Filter {
    public FilterConfig filterConfig;
	// 필터 초기화 하는 과정
	public void init(FilterConfig fConfig) throws ServletException {
		this.filterConfig = fConfig;
	}
	
	// 필터 종료될 때
	public void destroy() {
	}
	
	// 필터 동작과정
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
		///////////////////////////////////////// 서버에 가기전 필요한 코드 작성
		System.out.println("서블릿 전1");
		// 다음 필터로 전달!!!!
		// 다음 필터가 없으면 서블릿으로 간다!
		String encoding = this.filterConfig.getInitParameter("encoding");
		request.setCharacterEncoding(encoding);
		chain.doFilter(request, response); 
		///////////////////////////////////////// 사용자에게 가기전 필요한 코드 작성
		System.out.println("서블릿 후1");
	}

}
```

기본 생성된 템플릿에서 destroy(), doFilter(), init() 을 제외하고는 전부 깔끔하게 지운다.

우선 init()은 필터를 초기화하는 과정이다. 그리고 destroy()는 필터가 종료될 때의 과정이다. 마지막으로 doFilter()는 필터 동작 과정이다.

그러면 왜 doFilter()에 reqeust, response가 둘 다 존재하는 것일까? Filter라는 것은 클라이언트와 서버가 있을 때, 클라이언트에서 서버로 갈 때에만 Filter가 적용되는 것이 아니고, 반대로 서버에서 클라이언트로 나올 때에도 Filter가 쓰인다고 했었다. 그래서 이 Filter 안에서 request와 response를 다 다룰 수 있어야 하는 것이다.

그리고 FilterChain이라는 것이 들어오는데, '다음 필터로'인 것이다. 그런데 만약 다음 필터가 없으면 서블릿으로 간다.

즉, ```chain.doFilter(request, response);```는 무조건 있어야 하는 것이다. 이것은 '다음 필터로 전달'이라는 뜻인데, 다음 필터가 없으면 서블릿으로 간다. 그래서 이것을 가시적으로 확인하기 위해 "서블릿 전1", "서블릿 후1"을 출력해볼 것이다.

그러면 위 코드에서, 지금 필터의 설정들이 ```FilterConfig fConfig```에 들어올 것이다. 그러면 이전에 등록한 인코딩이 fConfig에 들어가 있을 것이다. 그런데 현재 코드에는 fConfig가 따로 없다. request에는 있다. 그래서 여기에 ```request.setCharacterEncoding("UTF-16");```을 입력하여 바꿀 것이다. 그런데 이렇게 설정을 직접적으로 명시하는 것은 좋지 않다. (한 곳에 모아놓고 처리하는 것이 좋다.) 그래서 ```public FilterConfig filterConfig;```를 입력하여 필드로 선언한다. 그리고 ```this.filterConfig = fConfig;```를 입력한다. init 메서드가 가장 먼저 동작을 할 것이니, 여기에 들어왔을 때 이 설정같은 것들이 내가 가지고 있는 변수에 집어넣으면서 초기화를 할 수 있는 것이다. 이렇게 하여 doFilter에서는 그것들을 쓸 수 있게 된다. 그리고 ```String encoding = this.filterConfig.getInitParameter("encoding");```를 입력하고, ```request.setCharacterEncoding(encoding);```로 수정한다. 그러고 나서 request, response를 달고 ```chain.doFilter()```을 한다.

그러면 이것을 눈으로 확인하기 위해서, 서블릿을 하나 만들 것이다. Create Servlet으로 들어가서 package명은 com.ssafy.servlet으로 하고, 클래스 이름은 MyServlet으로 하겠다. 그러고 나서 주석으로 달린 것들은 전부 지우고, doGet 메서드만 남긴다.

MyServlet 클래스의 코드는 다음과 같다.

```java
package com.ssafy.servlet;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;

public class MyServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		System.out.println(request.getCharacterEncoding());
	}
}
```

여기에서 ```System.out.println(request.getCharacterEncoding());```을 입력하여 request가 가지고 있는 CharacterEncoding을 get 하여 찍어본다. 그래서 UTF-16이 나오면 필터가 정상적으로 동작을 하고 있는 것을 알 수 있다.

Run on Server을 하면 다음과 같이 출력된다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7aee38fc-59cf-4058-828f-c72339dde26f" width="600px" />

그런데 "UTF-16"라는 것이 콘솔에 뜨지 않는다. 안 나오는 이유는 무엇일까? "서블릿 전1", "서블릿 후1"를 보니까 필터는 간 것 같다. 정답은 서블릿으로 안 간 것이다.

서블릿을 등록했을 때, web.xml을 보면 경로를 "/MyServlet"으로 해서 요청이 왔을 때에만 서블릿이 동작을 하게 해놨다. 그래서 이것은 안 뜨는 게 맞는 것이다.

주소 뒷부분에 "/MyServlet"을 붙여야 콘솔에 다음과 같이 "UTF-16"이 나타난다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/cc6b1c81-76a1-4fdb-8d07-0717325948ff" width="500px" />

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/276d0dba-1736-48d2-84f2-ab86e41ccf8e" width="600px" />

<br>

그리고 Filter 사용의 순서를 지정해보고 싶다면 다음과 같이 하면 된다.

우선 MyFilter2를 생성한다. MyFilter2의 코드는 다음과 같다.

```java
package com.ssafy.mvc.filter;

import java.io.IOException;

import jakarta.servlet.Filter;
import jakarta.servlet.FilterChain;
import jakarta.servlet.FilterConfig;
import jakarta.servlet.ServletException;
import jakarta.servlet.ServletRequest;
import jakarta.servlet.ServletResponse;
import jakarta.servlet.http.HttpFilter;

public class MyFilter2 extends HttpFilter implements Filter {
	// 필터 초기화 하는 과정
	public void init(FilterConfig fConfig) throws ServletException {
	}
	
	// 필터 종료될 때
	public void destroy() {
	}
	
	// 필터 동작과정
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
		/////////////////////////////////////////서버에 가기 전 필요한 코드 작성
		System.out.println("서블릿 전2");
		chain.doFilter(request, response); 
		/////////////////////////////////////////사용자에게 가기 전 필요한 코드 작성
		System.out.println("서블릿 후2");
	}

}
```

그리고 web.xml에는 다음과 같은 코드를 추가한다.

```xml
<filter>
    <filter-name>MyFilter2</filter-name>
    <filter-class>com.ssafy.mvc.filter.MyFilter2</filter-class>
</filter>
<filter-mapping>
    <filter-name>MyFilter2</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

그러고 다시 한 번 실행을 시키보겠다. 이 MyServlet까지 바로 요청을 보내보고 싶다면 MyServlet에 마우스 우클릭을 하고 Run on Server을 해도 된다. (그런데 이렇게 하는 방법은 권장하지 않는다. 그냥 테스트 용도로만 쓰겠다.)

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/366dd98f-4241-4f5d-aa75-d30827787122" width="600px" />

등록한 대로, "서블릿 전1", "서블릿 전2", "UTF-16", "서블릿 후2", "서블릿 후1" 순으로 출력된다.

클라이언트, 서버가 있을 때, 둘 사이에 필터 두 개(MyFilter, MyFilter2)를 두었다. 그래서 요청을 날렸을 대, 쭉 가면서 doFilter 이전에 "서블릿 전1"이 찍히고 doFilter가 동작한 다음, MyFilter2의 doFilter 이전에 "서블릿 전2"가 찍히고 doFilter이 동작했다. 그리고 나서 더 이상 Filter가 없으니 온전한 서블릿으로 들어갔고, 거기서 "UTF-16"을 출력했다. 그리고 다시 클라이언트로 돌아오면서 순서대로 "서블릿 후2", "서블릿 후1"가 찍혔다.

<br>

등록을 하기 위해서는 매핑 순서가 굉장히 중요하다. 

```xml
<filter-mapping>
    <filter-name>MyFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
<filter-mapping>
    <filter-name>MyFilter2</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

만약 위 코드에서 매핑 순서가 바뀌면, 출력되는 것도 2-1-1-2 순으로 출력될 것이다. 이렇게 순서를 조절할 수 있다.

그리고 doFilter는 필수적으로 작성해야 하는 부분이다.

<br>

이제는 클라이언트와 서버 사이에 필터, 디스패처서블릿이 추가된 그림으로 MVC 요청 흐름 설명을 할 수 있어야 한다.