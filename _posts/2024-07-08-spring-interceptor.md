---
layout: post
title: Interceptor
categories: Spring-SpringBoot
date: 2024-07-08 21:58:00 +0900
---
Interceptor에 대해서 살펴보겠다.

공놀이의 예를 들어보자. A와 B가 공을 주고 받고 있을 때, C가 나타나서 중간에서 공을 빼앗으려 하는 것이다. 여기서 공을 빼앗은 행위를 'intercept'라고 한다. 

인터셉트라는 것은 사용자가 요청을 던졌을 때, 그것을 가로채서 처리하고 다시 돌려주는 것이다. 필요하다면 그냥 빼앗아서 없애 버리기도 한다.

이렇게 보면 필터와 비슷해 보인다. Interceptor는 Filter의 상위 버전이다. 이때 상위 버전이란 '위에 있다'는 것이 아니라 기능이 좀 더 많다는 뜻이다.

<br>

## Interceptor

Interceptor의 특징은 다음과 같다.

* HandlerInterceptor를 구현한 것
* 요청(request)을 처리하는 과정에서 요청을 가로채서 처리
* 접근 제어(Auth), 로그(Log) 등 비즈니스 로직과 구분되는 반복적이고 부수적인 로직 처리
* HandlerInterceptor의 주요 메서드
    - preHandle()
    - postHandle()
    - afterCompletion()

'HandlerInterceptor를 구현한 것'이라는 것을 통해 HandlerInterceptor가 인터페이스라는 것을 추론할 수 있다.

어떤 게시판 서비스가 있을 때, 여기에는 조회, 등록, 수정, 삭제 등의 기능들이 있다고 가정해보자. 게시글 조회까지는 아무나 할 수 있지만, 등록, 수정, 삭제 요청은 아무나 하면 안 된다. 이러한 요청이 넘어왔을 때에는 Interceptor가 이 요청을 가로채서 "너 로그인 했어?" 라고 물어보고, 로그인을 안 했으면 그 요청을 없애버리는 것이다. 이러한 것을 'Interceptor'라고 한다.

이러한 인터셉터는 주요 메서드가 3가지가 있다. preHandle()은 이전에 해주는 것, postHandle()은 요청이 끝나고 나서, afterCompletion()은 모든 게 끝나고 나서 진행되는 것으로 짐작할 수 있다.

<br>

그러면 요청을 하나씩 뜯어 보겠다.

### preHandle

```java
@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
        throws Exception {
```

* Controller(핸들러) 실행 이전에 호출
* true를 반환하면 계속 진행한다.
* false를 반환하면 요청을 종료한다.

preHandle의 반환값이 boolean 형이기 때문에, true or false를 반환한다. 이때 false를 반환하면 그 즉시 요청을 종료하는데, 바로 종료하는 게 아니라 어딘가로 페이지를 돌려버리는(이동시켜버리는) 것이다. Ex&#41; 로그인이 필요한 서비스였다면 로그인 페이지로 돌리는 것이다.

그래서 인자로는 request, response가 들어오고, handler라고 하는 '지금 내가 어디 컨트롤러로 가야 하는지 알 수 있는' 객체도 들어온다.

<br>

### postHandle

```java
@Override
public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
        ModelAndView modelAndView) throws Exception {
```

* Controller(핸들러) 실행 후 호출
* 정상 실행 후 추가 기능 구현 시 사용
* Controller에서 예외 발생 시 해당 메서드는 실행되지 않음

request, response가 있고, 어떤 핸들러로 메서드를 실행시키기 위해 handler가 인자로 있다. 그런데 추가로 modelAndView가 인자로 들어왔다. DispatcherServlet 에서 컨트롤러를 찍고, 필요하다면 무언가를 처리하고 다시 돌아올 때 ModelAndView를 담아서 가져온다. 담아왔던 view이름을 가지고 ViewResolver에게 "이거 view 어디 있나요?" 하고 물어보는 것이다.

postHandle이라는 것은 컨트롤러를 찍고 온 것이기 때문에 ModelAndView 객체를 쓸 수 있다. 만들어져 있으니까. preHandle은 없다.

정상적으로 실행했을 때 추가 기능이 필요하다면 이렇게 구현할 수 있다.

만약 Controller에서 예외가 터졌으면, 정상적으로 요청의 흐름이 가지 않았다는 것이니 해당 메서드는 실행되지 않는다. 그러니까 postHandle은 예외 없이 정상적으로 실행이 되었을 때 실행이 되는 것이다.

<br>

### afterCompletion

```java
@Override
public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception e)
        throws Exception {
```

* 뷰가 클라이언트에게 응답을 전송한 뒤 실행
* Controller 에서 예외 발생 시, 네 번째 파라미터로 전달이 된다. (기본은 null)
* 따라서 Controller에서 발생한 예외 혹은 실행 시간 같은 것들을 기록하는 등 후처리 시 주로 사용.

모든 게 끝나고 나서 실행되는 메서드이다. request, response가 모두 OK이니까 (사용자에게 다 줘버리고 난 후이니까) ModelAndView는 없다. 그래도 어떤 handler에서 왔는지는 알려준다. 그리고 마지막으로 Exception 객체가 네 번째 파라미터로 있다.

만약 예외가 발생하지 않으면 이 Exception 파라미터는 null 이다. 예외가 만약 터졌다면 이제는 어떤 예외 객체를 가지고 있는 것이다.

postHandle은 예외가 터졌을 때 정상적으로 실행되지 않지만, afterCompletion은 무조건 실행이 된다. 그래서 Controller에서 발생한 예외 혹은 실행 시간 같은 것들을 기록하는 등 후처리 시 주로 사용되는 것이다.

그런데 우리가 코드를 짜면서 실제로는 preHandle만 주로 쓰게 될 것이다. postHandle, afterCompletion을 생각보다 쓸 일이 없다. 쓸 곳이 없다. 서비스를 가지고 실행 시간 등을 기록할 것이 아니기 때문이다. 하지만 알고 있어야 한다.

<br>

### Interceptor 흐름

그래서 Interceptor의 흐름을 도식화하면 다음과 같다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/d821493c-7338-47f6-90d9-798f77ba1026" width="550px" />

DispatcherServlet에서 요청이 들어왔을 때, 제일 먼저 컨트롤러로 가기 전에 preHandle라는 (인터페이스를 구현한) 인터셉터가 먼저 메서드를 실행한다. 그리고 여기서 true 혹은 false 를 반환한다. false이면 Controller로 가지 않는다. true인 경우에만 DispatcherServlet에서 Controller로 진행을 한다.

진행을 하고 무언가 응답을 받았다. 만약 이 상황이 정상적으로 동작 하였다면 postHandle을 실행한다. 그런데 만약 Controller에서 DispatcherServlet으로 가는 진행 상황이 정상적으로 동작하지 않고 예외가 터지면 postHandle을 실행하지 않는다.

그리고, (위에 다 모르겠고) 사용자가 View 까지 전달된 이후에 정상적으로 실행이 되었다면 afterCompletion이 OK이다. 이때 네 번째 인자의 Exception은 null 이다. 무언가 예외가 터졌더라도 afterCompletion은 처리가 된다. 이때 네 번째 인자인 Exception은 null이 아니다.

<br>

### Interceptor 등록 (Servlet-context.xml)

* &#60;interceptors&#62; 태그 안에 &#60;interceptor&#62; 태그로 등록 가능
* 매핑할 URL을 지정할 수 있고, 제외할 URL 또한 지정할 수 있음.

```xml
<interceptors>
    <interceptor>
        <mapping path="/*"/>
        <beans: bean class="com.ssafy.mvc.interceptor.AInterceptor"/>
    </interceptor>
</interceptors>
```

이제 Interceptor를 등록해야 하는데, Interceptor은 web과 관련이 있을까 없을까? 관련이 있다. 왜냐하면 요청이 오면서 그 요청을 가로채서 무언가 처리를 하는 것이기 때문이다.

그러면 MVC에서, DispatcherServlet에서는 설정 파일을 두 개 가지고 한다고 했다. servlet-context.xml 과 root-context.xml 중 Interceptor은 web과 관련돼 있으니 Servlet-context.xml에 등록해야 한다.

매핑할 URL을 지정하고, 제외할 URL을 지정할 수 있다. 어떠한 요청이 왔을 때에만 이 Interceptor가 동작하게 할 수 있고, 모든 요청을 다 처리할 것인데 이러이러한 것은 처리해 주라고 제외를 할 수 있다.

스프링에서 사용하는 PathPattern은 <a href="https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/util/pattern/PathPattern.html" alt="Spring PathPattern">해당 링크</a>를 참고하면 된다.

<br>

### Interceptor 체이닝

* 여러 개의 인터셉터를 동시에 등록할 수 있음.
* 작성 순서에 따라 동작

```xml
<interceptors>
    <interceptor>
        <mapping path="/*"/>
        <beans:bean class="com.ssafy.mvc.interceptor.AInterceptor"/>
    </interceptor>
    <interceptor>
        <mapping path="/*"/>
        <beans:bean class="com.ssafy.mvc.interceptor.BInterceptor"/>
    </interceptor>
    <interceptor>
        <mapping path="/*"/>
        <beans:bean class="com.ssafy.mvc.interceptor.CInterceptor"/>
    </interceptor>
</interceptors>
```

인터셉터도 체이닝을 해서 쓸 수 있다. InterceptorA, InterceptorB, InterceptorC, ... 와 같은 방식으로 등록해 사용할 수 있는 것이다.

<br>

이제 실습을 해보겠다. 깃에서 Spring_Legacy_Template을 import 해주겠다. 그리고 이름을 "Spring_04_Interceptor"로 변경한다.

pom.xml에 가서 &#60;groudId&#62;, &#60;artifactId&#62;도 "Spring_04_Interceptor"로 변경한다.

그리고 src/main/webapp/WEB-INF/spring/appServlet 폴더 안에 servlet-context.xml 이 있다. 

<img src="https://github.com/user-attachments/assets/55e91696-d79b-4bd3-af88-0ef0f5c5c563" width="320px"/>

이것은 옛날에 Spring 을 이용해서 MVC 프로젝트를 만들면 다 이런 식으로 만들었기 때문에, 나중에 현업에 가서 이런 것을 볼 확률이 높다. SpringBoot를 사용한다면 전혀 다른 구조로 배우겠지만, 지금은 옛날 방식으로 진행을 해볼 것이다.

위 그림을 보면 servlet-context.xml 이라는 설정 파일이 있고, 바깥에 root-context.xml 설정 파일이 있다.

이런 설정 파일들은 여러 개 만들 수 있다. 그런데 굳이 만들지는 않는다.

<br>

그리고 views 폴더 안에 화면들이 저장될 것이고, src/main/java 안에 컨트롤러들이 처리될 것이다.

servlet-context.xml 파일을 보면 다음과 같다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="http://www.springframework.org/schema/mvc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:beans="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd
		http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

	<!-- DispatcherServlet Context: defines this servlet's request-processing infrastructure -->
	
	<!-- Enables the Spring MVC @Controller programming model -->
	<annotation-driven />

	<!-- Handles HTTP GET requests for /resources/** by efficiently serving up static resources in the ${webappRoot}/resources directory -->
	<resources mapping="/resources/**" location="/resources/" />

	<!-- Resolves views selected for rendering by @Controllers to .jsp resources in the /WEB-INF/views directory -->
	<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<beans:property name="prefix" value="/WEB-INF/views/" />
		<beans:property name="suffix" value=".jsp" />
	</beans:bean>
	
	<context:component-scan base-package="com.ssafy.mvc.controller" />

	
</beans:beans>
```

위 코드를 간단히 보자. "annotation-driven"을 넣어줘야 수월하게 사용할 수 있다. 그리고 AOP를 쓰려면 무언가 하나 추가해야 한다. 그리고 ```<resources mapping="/resources/**" location="/resources/" />```가 없으면 모든 경로를 서블릿이 처리해버린다. webapp 안에 resources 폴더를 나중에 하나 만들고, 요청이 앞에 "/resources"가 붙어있으면 webapp/resources 에서 찾아줄 것이다. 여기에는 CSS, image 등이 다 저장될 것이다. 이 부분은 추후에 FileUpload를 하면서 다뤄볼 것이다. 그리고 ViewResolver 등록하고, component-scan으로 컨트롤러가 등록되어 있다.

이제 Spring_04_Interceptor 을 Run on Server로 실행시키면 화면에 "Hello Legacy"라고 뜬다.

HelloController.java 를 보면 GetMapping으로 "/" 요청이 왔을 때 index로 튕기게 했다.

```java
package com.ssafy.mvc.controller;

import org.springframework.stereotype.Controller;

@Controller
public class HelloController {
    @GetMapping("/")
    public String index() {
        return "index";
    }
}
```

index.jsp의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h2>Hello Legacy</h2>
</body>
</html>
```

여기까지는 문제 없다. 

<br>

이제 HelloController의 이름을 MainController로 변경한다. 그리고 com.ssafy.mvc.interceptor 패키지를 생성하고 그 안에 AInterceptor 클래스를 생성한다.

인터셉터는 HandlerInterceptor를 구현한 것이어야 한다. AInterceptor 클래스의 코드는 다음과 같다.

```java
package com.ssafy.mvc.interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

// 인터셉터는 HandlerInterceptor 구현한 것!
public class AInterceptor implements HandlerInterceptor {
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {
		// boolean 형태의 반환타입 true 진행 / false 종료
		System.out.println("A : preHandle");
		return true;
	}

	@Override
	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
			ModelAndView modelAndView) throws Exception {
		// 정상 수행 후 실행, 예외 발생시 미실행
		System.out.println("A : postHandle");
	}

	@Override
	public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
			throws Exception {
		// View가 전달된 후 실행 (무조건) 예외 미 발생시 객체는 null 로 초기화
		System.out.println("A : afterCompletion");
	}
}
```

<kbd>Ctrl + Space</kbd>를 누르면 preHandle(), postHandle(), afterCompletion() 등을 쓸 수 있다고 나와있다. 

그러면 자동완성을 이용해서 preHandle(), postHandle(), afterCompletion() 을 가져온다.

우선 preHandle() 은 boolean 형태의 반환타입을 가지고 있었다. 그래서 true면 진행, false면 종료를 한다. 그런데 연습이니까 그냥 무조건 true라고 하고 A 인터셉터에서 preHandle()이 동작했다는 것을 출력할 것이다.

그리고 postHandle()을 보면 request, response, handler, ModelAndView 가 파라미터로 있었다. 마찬가지로 postHandle() 에도 "A : postHandle"을 출력한다. postHandle의 경우 정상적으로 수행 후 실행되고, 예외가 발생하면 미 실행된다.

afterCompletion() 에서도 "A : afterCompletion"을 출력한다. afterCompletion의 경우 사용자에게 View까지 전달된 후 실행된다. 무조건 실행되는 것이고, 예외 미 발생 시 객체는 null로 초기화 된다.

<br>

이제 servlet-context.xml 을 보겠다. 여기에 작성을 해야 하는데, AInterceptor을 빈으로 등록해야 할까? 빈으로 등록하는 방법에는 여러 가지가 있다. 이번에는 직접 등록을 하겠다. 등록하는 코드는 다음과 같다.

```xml
<interceptors>
    <interceptor>
        <mapping path="/*"/>
        <beans:bean class="com.ssafy.mvc.interceptor.AInterceptor"/>
    </interceptor>
</interceptors>
```

bean의 클래스를 "com.ssafy.mvc.interceptor.AInterceptor"로 등록했다. 그리고 mapping을 할 것인데 모든 경로에 매핑을 하였다. mapping을 먼저 쓰면 빨간 줄이 없어진다.

<br>

이제 Run on Server을 하면 다음과 같이 출력된다.

<img src="https://github.com/user-attachments/assets/f5f82a90-26d7-4b1e-816d-298daf175361" width="700px" />

잘 출력되었다.

<br>

이제 BInterceptor, CInterceptor 클래스를 생성한다. (앞서 만든 AInterceptor를 복사 붙여넣기 하고, 안의 내용만 수정한다.)

그리고 servlet-context.xml에 다음과 같이 전부 등록한다.

```xml
<interceptors>
    <interceptor>
        <mapping path="/*"/>
        <beans:bean class="com.ssafy.mvc.interceptor.AInterceptor"/>
    </interceptor>
    <interceptor>
        <mapping path="/*"/>
        <beans:bean class="com.ssafy.mvc.interceptor.BInterceptor"/>
    </interceptor>
    <interceptor>
        <mapping path="/*"/>
        <beans:bean class="com.ssafy.mvc.interceptor.CInterceptor"/>
    </interceptor>
</interceptors>
```

이제 다시 Run on Server을 하겠다. 그러면 다음과 같이 출력된다.

<img src="https://github.com/user-attachments/assets/1edfb17a-7c58-4c85-b468-36764ad996cb" width="700px" />

ABC-CBA-CBA 순으로 나왔다. 우선 ABC 순으로 등록했으니 이건 오케이이다. 그리고 튀어나올 때에는 CBA로 나오는 것도 이해가 된다. 그러고 나서 CBA로 동작하는 것이다.

<br>

## Login Interceptor 실습

이제 로그인 인터셉터를 만들어보겠다.

우선 로그인 페이지를 하나 만들어야 한다. src/main/webapp/WEB-INF/views 폴더 안에 login.jsp를 생성한다. login.jsp 의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Login</title>
</head>
<body>
	<h2>로그인 페이지</h2>
	
	<form action="login" method="POST">
		<input type="text" name="id"> <br>
		<input type="password" name="password"> <br>
		<button>로그인</button>
	</form>
	
</body>
</html>
```

예전에는 로그인이라는 기능이 그냥 메인 페이지 한쪽 구석에 있었다. 그런데 어느 시점부터 '로그인 페이지로 가기'로 해서 로그인 페이지로 바뀌어 버렸다. 이제 보안, 트렌드 때문에 형식이 바뀐 것이다.

"login"이라는 요청을 보낼 것인데 method는 "POST"로 할 것이다. 그리고 type은 "text"이고 name은 "id", type은 "password"이고 name은 "password"로 위와 같이 만들었다. 그리고 로그인 버튼을 만들었다.

<br>

이번에는 index.jsp를 수정해보겠다. index.jsp의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="jakarta.tags.core" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h2>인터셉터</h2>
	<c:choose>
		<c:when test="${empty id}">
			<div><a href="login">로그인 페이지</a></div>
		</c:when>
		<c:otherwise>
			<div>${id} 님 반갑습니다. <a href="logout">로그아웃</a></div>
		</c:otherwise>
	</c:choose>
	
	<div>
		<a href="regist">게시글 등록페이지</a>
	</div>
</body>
</html>
```

로그인을 했다면 "OO님, 반갑습니다. 로그아웃 하실래요?"와 같은 것이 있고, 만약 로그인을 안 했으면 "로그인 할래요?"와 같은 것이 있을 수 있다.

원래 같았으면 자바 코드로 if 를 사용했을텐데, 이제는 JSTL을 활용해보겠다. 우선 위에 ```<%@ taglib prefix="c" uri="jakarta.tags.core" %>```를 입력한다.

c:if를 두 번 쓰든지, c:choose를 쓸 수 있다. 그래서 EL 표현방식으로, id가 empty라면 a 태그를 이용해서 로그인 페이지로 가게 하였다.

그게 아니라면 c:otherwise (즉, else)를 이용해서 "{id}님 반갑습니다"가 출력되게 하는 것이다. 그리고 로그아웃 버튼을 만들었다.

<br>

그리고 regist.jsp를 생성한다. regist.jsp의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h2>게시글 등록페이지</h2>
	<!-- 내용은 생략 -->
</body>
</html>
```

<br>

위의 내용은 스프링은 전혀 없이 JSTL 까지의 내용이니까 어려움이 없다.

이제 여기서 "login"을 누르면 갈 수 있는 것을 만들어야 한다. Run on Server을 하여 켰을 때, '로그인 페이지'를 누르면 http://localhost:8080/mvc/ 에서 http://localhost:8080/mvc/login 으로 'login'이 붙는다. 마지막 슬래시(/)에 'login'이 붙는 것이다.

<img src="https://github.com/user-attachments/assets/9184aff1-cb32-46c1-b238-0a0840b509db" width="300px" />

<br>

이제 MainController로 와서 이것을 처리해줘야 한다. MainController의 코드는 다음과 같다.

```java
package com.ssafy.mvc.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;

import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpSession;

@Controller
public class MainController {
	@GetMapping("/")
	public String index() {
		return "index";
	}

	@GetMapping("/login")
	public String loginForm() {
		return "login";
	}

	@PostMapping("/login")
	public String login(HttpSession session, @RequestParam("id") String id, @RequestParam("password") String password) {
//		HttpSession session = request.getSession();
		// UserService라는것을 만들어서 호출 한다. 
		if(id.equals("ssafy") && password.equals("1234")) {
			// 로그인 성공!
			session.setAttribute("id", id);
//			return "index"; // 포워딩
			return "redirect:/";
		}
		// 아니라면 다시 로그인페이지로 간다.
		return "redirect:/login";
	}
	
	
	@GetMapping("/logout")
	public String logout(HttpSession session) {
//		session.removeAttribute("id"); id 지우기
		session.invalidate();
		
		return "redirect:/";
	}
	
	@GetMapping("/regist")
	public String registForm() {
		return "regist";
	}
	
}
```

우선 ```@GetMapping("/login")```을 입력하여 'login'이라는 요청이 왔을 때 loginForm()으로 "login" 페이지에 가도록 하였다. 그래서 WEB-INF/views/login.jsp 에 가게 하는 것이다.

Run on Server을 한 상태에서 '로그인 페이지'를 클릭하면 이제 다음과 같이 화면에 나타난다.

<img src="https://github.com/user-attachments/assets/90934ded-bf31-4bd1-a954-0c8cd5726c6a" width="300px" />

<br>

그럼 여기에다가 "ssafy", "1234"를 누르고 '로그인'을 클릭하면 현재 POST를 지원하지 않는다며 405 에러가 화면에 나타난다.

이것은 '그러한 요청을 처리할 수 있는 매핑은 있는데 방식이 잘못되었다'는 의미이다. login.jsp 에서 "login"이라는 요청을 보낼 것이고 그게 "POST" 라고 해서 DispatcherServlet이, HandlerMapping이 봤더니 "/login"이 있는데 이게 @GetMapping 이다. 그러니까 POST를 처리할 수 없다는 차원에서 405 에러 - '허용되지 않는 메소드'라고 뜨는 것이다. (결국에는 이것도 404이다. 400대 에러는 우리의 잘못이다.)

그래서 위와 같이 ```@PostMapping("/login")```을 입력하여 코드를 추가한다. 그런데 만약 인자 설정을 ```login(String id, String password)```으로 해놓으면 아이디와 패스워드를 잘 쓸 수 있다. 여기 있는 id 와 login.jsp의 ```name="id"```의 "id"가 일치하면 잘 가져오라고 설정을 해놓았기 때문이다. 이 설정은 pom.xml에 다음과 같이 있다.

```xml
<compilerArgs>
    <arg>-parameters</arg>
</compilerArgs>
```

그런데 이러한 방식은 권장되지 않는다. 명확하게 써줘야 좋다.

그래서 ```login(@RequestParam("id") String id, @RequestParam("password") String password)``` 의 방식으로 수정한다. 이렇게 입력받은 것을 통해서 실제로는 UserService를 만들어서 호출한다. 

그런데 이것이 지금 없다. 그러므로 ```if (id.equals("ssafy") && password.equals("1234")) {```를 입력하여 이렇게 아이디, 패스워드를 입력하면 통과를 시키는 것이다. (차라리 이렇게 쓸 것이면 "ssafy"를 먼저 쓰는 것이 나을 수도 있다. id 를 안 넘겨줬을 때 id 가 null 이 되니까 'null은 equals가 없어' 하면서 에러가 터져 버리는 것이다. 그런데 사실 이렇게는 앞으로 짜지 않을 것이니 굳이 고칠 필요는 없다. 그래도 선후 관계에 대해서 생각은 해봐야 한다.)

일단 이 관문을 통과하면 로그인 성공이다. 로그인을 성공했으면 그것을 session 에 저장했다. 지금까지는 session 을 가져오기 위해서 HttpServletRequest를 사용했다. 만약 인자에 ```HttpServletRequest request```를 추가하면 다음 줄에 ```HttpSession session = request.getSession();```을 추가해서 가져와야 했다. 그런데 굳이 이런 식으로 하지 않아도 된다. 이렇게 가져오는 게 아니라, HttpSession으로 가져오는 것이다.

그래서 ```login(HttpSession session, @RequestParam("id") String id, @RequestParam("password") String password) {```로 코드를 수정한다. 그리고 아래에 ```session.setAttribute("id", id);```를 추가한다. 이때 앞의 "id"는 index.jsp의 "id"이다. 그리고 뒤의 id 는 위에 ```@RequestParam("id") String id``` 에서 넘겨받은 String id 이다. 그러면 ```@RequestParam("id")```의 "id"는 무엇일까? 이것은 login.jsp 페이지에서 넘겨받은 ```<input type="text" name="id">```에서의 "id" 이다. (굳이 다른 이름으로 쓰일 필요가 없어서 id 로 전부 다 써놨는데, 이렇게 썼을 때 구분을 잘 못하는 이슈가 있으니 명확히 구분할 수 있어야 한다.)

<br>

그리고 ```return "index";``` 를 입력한다. 이것은 포워딩(forwarding)이다. request와 response 영역을 그대로 가지고 보내는 것이다. 이때 index 페이지에서 비밀번호가 담겨있으니 꺼내 쓸 수 있다. 이것을 없애려면 리다이렉트(redirect) 방식으로 보내야 한다.

스프링에서 redirect는 다음과 같이 사용한다. ```return "redirect:/";``` 이때 ```redirect:index```와 같은 방식으로 기입하지 않는다. 여기에 index를 쓰지 않은 이유는, 현재 "index"라는 요청이 들어왔을 때 그것을 처리할 수 있는 매핑이 없기 때문이다. 그래서 그냥 "/"를 보내면 @GetMapping("/") 이 동작을 하면서 index 페이지로 간다.

그리고 그게 아니라면 ```return "redirect:/login";```을 입력하여 다시 로그인 페이지로 가게 하였다.

<br>

다시 Run on Server 을 하고, id에 "ssafy", pw에 "1234"를 입력한다. 그러면 다음과 같은 화면이 나타난다.

<img src="https://github.com/user-attachments/assets/7f076334-d040-480d-a4d9-d5298d31ba55" width="320px" />

<br>

그리고 "logout" 이라는 요청도 바로 처리해보겠다.

```@GetMapping("/logout")``` 이라는 요청이 왔다고 했을 때, 여기에는 HttpSession만 있으면 될 것 같고, 일단 ```session.removeAttribute("id");``` 의 방식으로 id라는 속성을 지우거나, ```session.invalidate();``` 로 지울 수도 있다. 그리고 ```return "redirect:/";```를 한다.

로그인 페이지를 클릭하고 이상한 것을 쓰면 다시 로그인 페이지로 튕긴다. 그리고 로그인을 한 다음 '로그아웃'을 하면 로그인이 잘 되었다.

<br>

그런데 아직 인터셉터를 사용하지 않았다. com.ssafy.mvc.interceptor 패키지에 LoginInterceptor 클래스를 생성한다. LoginInterceptor 클래스의 코드는 다음과 같다.

```java
package com.ssafy.mvc.interceptor;

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;

import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import jakarta.servlet.http.HttpSession;

@Component
public class LoginInterceptor implements HandlerInterceptor {
	
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {
		
		HttpSession session = request.getSession();
		
		if (session.getAttribute("id") == null) {
			response.sendRedirect("login");
			return false;
		}
		
		return true;
	}
}
```

우선 HandlerInterceptor를 implements 하게 한다. 그리고 여기에서는 preHandle() 정도만 쓰면 된다. 

preHandle, 어떤 요청이 나갔을 때, 세션을 뒤져서 id가 있는지 없는지 확인해야 한다. 그 id가 실제 유저인지는 중요하지 않다. 그 id가 있으니까 세션에 저장을 했을 것이고, 우리가 그것을 쓰는 것이다.

```HttpSession session = request.getSession();```을 하여 가져온다. 그리고 ```session.getAttribute("id")``` 를 하여 id를 가져왔는데 그게 만약에 null 이면 로그인을 하지 않았다는 것이다. 그러면 다시 "login" 페이지로 redirect 하고, return false로 해당 요청은 종료시킨다.

<br>

servlet-context.xml 로 와서 일단 이전에 했던 AInterceptor, BInterceptor, CInterceptor은 주석 처리를 한다.

여기에서 등록을 해야 하니까, ```<context:component-scan base-package="com.ssafy.mvc.controller, com.ssafy.mvc.interceptor" />``` 와 같이 "com.ssafy.mvc.interceptor"를 추가한다. 해당 패키지도 내가 컴포넌트 스캔을 하겠다는 것이다.

그리고 아래에 다음과 같이 interceptor을 등록한다.

```xml
<interceptors>
    <interceptor>
        <mapping path="/*"/>
        <exclude-mapping path="/login"/>
        <exclude-mapping path="/"/>
        <beans:ref bean="loginInterceptor"/>
    </interceptor>
</interceptors>
```

reference로는 "loginInterceptor"로 쓴다. 이렇게 명명이 된 이유는, 내가 따로 LoginInterceptor.java에서 ```@Component```을 등록했는데 이름을 명시하지 않아서 앞글자만 소문자로 바뀐 것이다.