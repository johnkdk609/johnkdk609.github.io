---
layout: post
title: JSP
categories: Spring-SpringBoot
date: 2024-04-02 16:52:00 +0900
---
이번에는 JSP에 대해 알아볼 것이다.

<br>

JSP를 알아보기 전에, Servlet이 무엇인지 다시 간략하게 알아보겠다. 

클라이언트가 있고 서버 컴퓨터가 있다고 했을 때, 클라이언트가 요청을 날린다. 그 요청에는 URL 경로가 있을 텐데, 이 경로로 매핑되어 있는 서블릿이 있을 것이다. (만약 매핑되어 있지 않으면 404 Error가 발생한다.) 

매핑이 되어 있다면 해당 서블릿이 동작을 하면서 GET 요청으로 들어왔는지, POST 요청으로 들어왔는지에 따라서 doGet이 동작할 수도 있고 doPost가 동작할 수도 있다. 

이런 식으로 동작을 시키고 로직을 출력해서 사용자에게 응답을 돌려준다. PrintWriter이라는 것을 이용해서 우리가 사용자에게 html을 동적으로 생성해서 돌려준 것이다. 

나아가 이제는 서블릿을 하나만 두고 내부적으로 여러 로직을 처리하도록 하는 Front Controller 패턴이 있었다. 그런데 내가 URL 경로를 딱 한 개만 쓴다면 내부적으로 요청이 구구단인지, 등록인지 구분을 할 수 있어야 한다. 이를 어떻게 하냐면 input 태그의 hidden이라는 타입으로 사용자는 모르게 보내는 것이다. 가령 ```action = "gugu"```라고 하는 이름으로 보내면, 받아서 ```req.getParameter("action")``` 메서드를 활용해 꺼낸 것을 받을 것을 가져다가 doRegist 등의 동작을 쭉 수행한 것이다.

<br>

## JSP (Java Server Pages)

요즘에는 'Java' 말고 'Jakarta'라고 쓴다. 저작권과 관련된 문제로 인해서이다.

<b>JSP는 Servlet 표준을 기반으로 작성된 웹 어플리케이션 개발 언어</b>이다. 서블릿이 Java 코드 안에 HTML 코드를 넣은 것이라면, JSP는 HTML 코드 안에 Java 코드를 작성하여 동적으로 웹 페이지를 생성하여 브라우저에게 돌려주는 페이지인 것이다.

Servlet에서 HTML 코드를 쓰는 것이 굉장히 불편했다. 그래서 HTML 내에 자바 코드를 쓰는 것이 더 편하지 않을까 하는 생각에 JSP가 등장한 것이다.

<u>JSP 파일은 실행 시에는 Servlet으로 변환된 후에 실행된다.</u> 내가 JSP를 만들었다 하더라도, 컴파일러가 내부적으로 Servlet이라는 ```.java``` 파일로 바꿔버리고 사용자에게 돌려주는 것이다. 즉, 내가 JSP를 쓴다고 하더라도 그것은 서블릿이다.

<br>

그러면 이번에도 Hello를 한 번 찍어보자.

STS에서 Dynamic Web Project를 생성할 것이다. Finish를 누르기 전에 Next를 두 번 누르면 Context root가 나온다. Context root가 무엇인지 설명할 수 있어야 한다. 내가 WAS라는 것에 프로젝트를 빌드해서 올릴 것인데, 프로젝트를 여러 개 올렸을 때 똑같은 경로에 매핑이 될 것을 구분하기 위해서 Context root를 사용했다. 만약 내가 프로젝트를 딱 하나만 올릴 것이라면 Context root를 그냥 ```/```로 처리해도 상관없다. 

나중에 스프링 부트를 하게 될 것인데, 스프링 부트는 이 프로젝트가 이미 톰캣을 내장하고 있다. 내가 스프링 부트 프로젝트를 실행시켰다는 것은 내가 가지고 있는 톰캣의 내 프로젝트 딱 하나만 실행한다는 것이니 스프링 부트는 기본적으로 Context root가 ```/```이다. (물론 Context root를 바꿀 수 있기는 하다.)

아무튼 지금 내가 쓰는 WAS는 내 전용의 WAS가 아니고 외부적으로 하나 만들고, 프로젝트를 하나 만들 때마다 올려두는 것이다. 그래서 Context root가 기본 default 값은 프로젝트 명으로 잡혀져 있고, 필요하다면 내가 마음대로 바꿔서 쓸 수 있는 것이다.

그리고 필요하다면 web.xml 을 만들어서 사용할 수 있다. (이번에는 web.xml을 따로 만들지 않고 해볼 것이다.)

<br>

사전 제공 코드를 사용하겠다. ```.jsp``` 파일들은 프로젝트 폴더의 webapp 안에 넣으면 된다.

hello.jsp 의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Hello</title>
</head>
<body>
	<%
	String name = "John";
	%>
	<h2>Hello JSP</h2>
	<h4>
		안녕하세요. <%=name%>입니다.
	</h4>

	<a href="index.html">홈으로</a>
</body>
</html>
```

위 코드에서, 맨 위에 ```<%@ page language="java", ... %>```가 없으면 그냥 html 파일이다. 그런데 이 코드가 붙음으로써 이 파일이 JSP가 되는 것이다.

위 코드에서 ```<%   %>```를 썼더니 그 안에 자바 코드가 들어가는 것을 알 수 있다. 그리고 ```<%=   %>``` 안에 이전에 쓴 자바 변수 등을 넣으니 출력되는 것을 볼 수 있다.

프로젝트를 Run 해도 되지만, 해당 jsp 파일에 우클릭을 하고 파일째로 서버를 실행시킬 수도 있다. (그런데 이것은 권장되는 방법은 아니다.)

실행시킨 화면은 다음과 같다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0525fad8-9191-4875-809f-1ca00b812881" width="450px" />

<br>

내가 저장한 변수 name이 화면에 출력되고 있다.

JSP는 결국에는 서블릿이다. JSP 파일이 위와 같이 생기기는 했지만, 실질적으로 변환 파일을 보면 다음과 같이 변한다.

```java
    out.write("\r\n");
    out.write("<!DOCTYPE html>\r\n");
    out.write("<html>\r\n");
    out.write("<head>\r\n");
    out.write("<meta charset=\"UTF-8\">\r\n");
    out.write("<title>JSP</title>\r\n");
    out.write("</head>\r\n");
    out.write("<body>\r\n");
    out.write(" ");

String name = "John";

    out.write("\r\n");
    out.write(" <h2>Hello JSP</h2>\r\n");
    out.write(" <h4>안녕하세요. ");
    out.print(name );
    out.write("입니다.</h4>\r\n");
    out.write("</body>\r\n");
    out.write("</html>");
```

위 변환된 서블릿에서 계속 보이는 ```out```은 printWriter이라고 보면 되고, ```\r\n```은 Window 때문에 붙는 것이니 크게 신경쓰지 않아도 된다.

자바 코드를 쓰는 부분은 실제로 자바 코드로 되어 있다.

원래는 전부 셀프로 이렇게 작성해야 해서 매우 불편했다. 그래서 HTML 안에서 자바 코드를 쓰는 JSP가 등장한 것이다.

<br>

## JSP 동작

JSP의 동작을 한 번 살펴보겠다.

클라이언트가 어떠한 요청을 날렸을 때, JSP가 Servlet으로 변환이 되고, compiler를 통해 클래스 파일로 만들어진 다음, 그 결과가 HTML이 되어서 사용자에게 응답으로 돌아온다.

이 요청을 보낼 때, 가령 "```01_Hello.jsp``` 줘!"라고 한 것이다. 그러면 그것과 관련된 URL 매핑이 되어 있는 것을 가져다가 자바 서블릿 파일로 변환하는 것이다. 그리고 일련의 과정을 거쳐 내부적으로 HTML 코드로 반환되어야 할 것이 <b>response라는 객체에 담겨서 사용자에게 전달되는 것</b>이다.

다시 말하면, 요청은 request 객체가 동작하는 것이고 응답은 response 객체가 동작하는 것이다. 이런 <b>request, response는 객체이자 통로이다.</b>

<br>

## JSP 구성요소

JSP의 구성요소는 크게 5가지로 얘기할 수 있다.

* <b>지시자 (Directive)</b>

JSP 페이지에 대한 설정 정보를 지정하기 위해서 사용한다.

* <b>스크립트 요소 : 스크립트릿(Scriptlet), 표현식(Expression), 선언부(Declaration)</b>

JSP에서 문서의 내용을 동적으로 생성하기 위해서 사용한다. 즉, 스크립트릿, 표현식, 선언부가 자바 코드를 쓰는 것이라고 보면 된다.

* <b>JSP 기본 객체</b>

요청 및 응답 관련 정보를 얻거나, 응답 결과를 만들기 위해서 사용한다. JSP 기본 객체로는 request, response, out(PrintWriter), page, session, Application 등 다양하게 있었다.

* <b>표현언어 (Expression Language)</b>

JSP를 좀 더 간결하게 작성하기 위해서 사용한다.

* <b>Action Tag 와 JSTL</b>

자주 사용하는 기능을 모아 미리 정의하여 Tag 형태로 작성한다. JSP에서 자바코드를 쉽게 작성할 수 있도록 사용한다. (Action Tag는 하지 않을 것이다.) 우리가 결국에는 자바 코드로 반복문을 돌리기 위해서 직접 for문을 써야 하는데, 이렇게 하면 지저분해진다. HTML 을 쓰는데 태그 형식으로 쓸 수 있지 않을까 하고 등장한 것이 JSTL 이다.

<br>

