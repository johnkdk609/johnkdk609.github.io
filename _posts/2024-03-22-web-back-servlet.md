---
layout: post
title: Servlet
categories: Spring-SpringBoot
date: 2024-03-22 18:30:00 +0900
---
서블릿(Servlet)에 대해 알아보겠다.

서블릿을 작동시켜 보려면, Java EE를 설치해야 하고 encoding 설정에서 전부 UTF-8로 변경해야 하며, Tomcat을 설치해야 한다.

먼저, HelloServlet이라는 클래스를 하나 작성해서, 서블릿이 무엇인지 간단히 알아보겠다. (Java EE로 switch 해서 작동시켜야 한다.)

```java
package com.ssafy.servlet;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;

	protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		response.setContentType("text/html;characterset=UTF-8");
		PrintWriter writer = response.getWriter();
//		writer.write("<html>");
//		writer.write("<head>");
//		writer.write("<title>Hello! SSAFY!</title>");
//		writer.write("</head>");
//		writer.write("<body>");
//		writer.write("<h1>Hello! Servlet!</h1>");
//		writer.write("<p>Hello! SSAFY</p>");
//		writer.write("</body>");
//		writer.write("</html>");
		
		writer.write("""
		<html>
		  <head><title>Hello! SSAFY!</title></head>
		  <body>
		    <h1>Hello! Servlet!</h1>
		  </body>
		</html>
		""");

	}
}
```

위 코드를 'Run on Server'를 클릭해 작동시킨 다음, 작동한 서버에서 ```/hello```라고 붙여서 요청을 보내면 다음과 같은 화면이 나타난다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/821e4987-91c9-4aa1-907c-76fab3754aa5" width="400">

정상적으로 작동하고 있다.

<br>

지금 어떤 일이 발생한 것인지 알아보겠다. 내가 'BackEnd_00_Hello' 라는 프로젝트를 만들었는데, WAS(Tomcat)에다가 프로젝트를 배포했다. WAS에 이 프로젝트가 올라간 것이다.

그러면 내가 있는 브라우저(ex. Chrome)에서 주소창에 <kbd>http://localhost:8080/BackEnd_00_Hello/hello</kbd>라고 입력하여 WAS에 날리는 것이다. WAS에게 날렸더니, WAS가 "어, 너 지금 /hello라는 요청을 했네. 그거 'Hello Servlet'이라는 친구가 해결을 할 수 있지"하는 것이다.

위 코드에서 ```doGet()``` 은 해당 주소로 들어갔을 때 get 요청이다. (주소로 들어가면 get 요청이다.) 이런 요청이 왔을 때, html 페이지를 하나 만들어서 사용자에게 응답으로 보내겠다는 것이다.

브라우저가 HTML 코드들을 받아서 위 출력 화면과 같은 것을 돌려주는 것이다.

<br>

원래는 위 코드에서 ```writer.write(~~)```의 방식으로 한 줄 한 줄 써야 했다. 그런데, 자바 15 버전이 되면서 'block string'이라고 해서 쌍따옴표(") 세 개로 시작과 끝을 정하고 엔터를 쓰면서 html 코드를 작성할 수 있게 되었다. (위 코드를 실행시키면 결과는 똑같다.)

<br>

## Servlet

Servlet은 Server + Applet의 합성어이다. Applet은 Application과 Let의 합성어이다. (Let은 '조각'을 뜻한다.) 즉, <b>서블릿이라는 것은 서버 내에서 돌아가는 작은 프로그램</b>이라고 생각하면 되겠다.

<b>Java 언어로 작성된 웹 어플리케이션의 서버 측 프로그램이고, 자바를 이용하여 웹페이지를 동적으로 생성할 수 있다.</b> 동적으로 생성한다는 것은, 이미 만들어져 있는 html이 아니라 이번에 만들어서 던져버리겠다는 것이다. 위 코드에서 봤던 것처럼, 동적으로 만들어서 보내는 것이다.

Servlet은 <u>자바 코드 안에 html을 포함한다.</u>

그런데 ```.java```라는 코드 안에 계속해서 html을 넣으려 하다 보니 생각보다 힘들다. 차라리 html 코드 안에 Java 코드를 넣으면 편할 수도 있겠다고 누군가가 생각했다. 그래서 등장한 것이 JSP이다.

<br>

이제 예시 코드로 알아보겠다.

자바 클래스 파일을 서블릿처럼 동작하게 하려면, jakarta의 Servlet 인터페이스를 implements 해야 한다.

```java
package com.ssafy.myservlet;

import java.io.IOException;

import jakarta.servlet.Servlet;
import jakarta.servlet.ServletConfig;
import jakarta.servlet.ServletException;
import jakarta.servlet.ServletRequest;
import jakarta.servlet.ServletResponse;

public class MyServlet1 implements Servlet {

	@Override
	public void destroy() {
		//서블릿이 소멸할때
	}

	@Override
	public ServletConfig getServletConfig() {
		//서블릿 설정 객체를 반환
		return null;
	}

	@Override
	public String getServletInfo() {
		//서블릿 정보를 반환
		return null;
	}

	@Override
	public void init(ServletConfig arg0) throws ServletException {
		//파라미터로 넘겨받은 서블릿 설정파일을 가지고 초기화 하는 작업
	}

	@Override
	public void service(ServletRequest arg0, ServletResponse arg1) throws ServletException, IOException {
		//요청과 응답을 처리하는 일
	}

}
```

Servlet 인터페이스를 implements 하면 위와 같이 여러 개의 메서드를 오버라이드(재정의) 해야 한다. 오버라이드 해야 하는 메서드가 상당히 많은 상황이다.

destroy(), getServletConfig(), getServletInto(), init() 과 같은 메서드들을 매번 재정의하는 것은 비효율적이다.

<br>

그래서 Servlet 인터페이스의 구현체로 GenericServlet이 있다. GenericServlet은 클래스이다. 이제 GenericServlet을 extends 해서 서블릿을 만들어보겠다.

```java
package com.ssafy.myservlet;

import java.io.IOException;

import jakarta.servlet.GenericServlet;
import jakarta.servlet.ServletException;
import jakarta.servlet.ServletRequest;
import jakarta.servlet.ServletResponse;

public class MyServlet2 extends GenericServlet {

	@Override
	public void service(ServletRequest arg0, ServletResponse arg1) throws ServletException, IOException {
		//요청과 응답 처리는 사용자가 해줘 (나머지는 이미 다 해놨으니)
	}

}
```

GenericServlet은 추상 클래스(abstract class)이기 때문에, 메서드를 정의해주어야 한다. 그러면 위와 같이 service() 메서드만 구현하면 된다. 앞서 그냥 Servlet 인터페이스를 implements 했을 때와 비교하면 구현할 것이 확연히 줄어든 상황이다.

나머지는 처리해 놨으니, 요청과 응답 처리는 사용자가 하라는 것이다. 요청이 들어왔을 때 어떻게 할지, 응답이 들어왔을 때 어떻게 할지를 정하면 되는 것이다.

<br>

그런데 우리는 주로 통신 규약으로 HTTP를 쓴다. HTTP를 이용해서 요청을 주고 받고 할 것인데, 그냥 HttpServlet을 extends 할 수 있다.

```java
package com.ssafy.myservlet;

import jakarta.servlet.http.HttpServlet;

public class MyServlet3 extends HttpServlet{

	private static final long serialVersionUID = 1L;
	
	//doXXXX 은 적어도 1개는 만들어어야 해
}
```
