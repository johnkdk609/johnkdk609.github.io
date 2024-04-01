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

public class MyServlet3 extends HttpServlet {

	private static final long serialVersionUID = 1L;
	
	//doXXXX 은 적어도 1개는 만들어야 한다.
}
```

처음에 노란 줄이 뜨는 이유는, HttpServlet이 직렬화(Serialization)와 관련해 추가해야할 것이 있기 때문이다. 그래서 ```private static final long serialVersionUID = 1L;```를 추가했다.

그리고 Http는 메서드 방식이 GET 요청, POST 요청 등이 있다. 사용자가 어떠한 요청을 보냈을 때 GET 방식이 있었고, POST 방식이 있었다.

이런 요청들을 처리하기 위한 메서드들이 쭉 있는데, doGet, doPost 같은 것들이 있다. 

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/79af4f6e-66ac-4eb1-89fa-72d1ec0f6973" width="1000px" />

doGet, doPost 등.. doXXXX 가 있어야 알아서 호출이 되니 적어도 내가 doXXXX은 한 개는 만들어야 한다.

<br>

서블릿을 등록하는 방법은 크게 두 가지가 있다.

옛날에는 web.xml에 등록하는 방법이 default였다가, 이후에 봤던 어노테이션을 이용하는 방법이 default가 되었다가, 지금은 web.xml 방식으로 회귀했다.

그런데 우리는 두 가지 방식 모두 알아볼 것이다.

만약 파일을 새로 생성할 때 Servlet 파일로 바로 만들면 web.xml에 자동으로 등록이 된다.

<br>

이제 doGet() 메서드를 직접 만들어보겠다.

```java
package com.ssafy.myservlet;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

public class MyServlet4 extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		response.setContentType("text/html;characterset=UTF-8");
		PrintWriter writer = response.getWriter();

		writer.write("""
				<html>
				  <head><title>Hello! SSAFY!</title></head>
				  <body>
				    <h1>Hello! MyServlet4!</h1>
				  </body>
				</html>
				""");
	}
}
```

처음에 'do'라고 입력하고 <kbd>Ctrl + Space</kbd>를 눌러보면 만들 수 있는 것들이 쭉 보인다. 이 중에서 doGet을 클릭하고, req를 request, res를 response로 변경한다. (이것은 그냥 보기 편하기 위함이다.)

그리고 이전에 했던 HelloServlet의 코드를 붙인다.

response는 응답과 관련이 있다. 내가 보낼 응답에다가 ```.setContentType("text/html;characterset=UTF-8")```을 붙여 "내가 text로 쓰면 브라우저는 html로 바꿔서 생각해라"는 것이다. 그리고 characterset를 UTF-8로 세팅을 해두라는 것이다. 사용자에게 보낼 response라는 통로에 인코딩 방식을 UTF-8로 알아서 세팅을 해놓은 것이다.

그리고 ```PrintWriter writer = response.getWriter();```의 코드는, PrintWriter라는 것을 이용해서 response에다가 getWriter()라는 메서드를 통해 객체를 가지고 온 것이다.

그리고 ```writer.write()```라는 메서드를 통해서 위와 같은 방식으로 작성을 하겠다는 것이다.

<br>

### Servlet 등록 방법 (web.xml)

이제 web.xml을 보겠다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="https://jakarta.ee/xml/ns/jakartaee" xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd" id="WebApp_ID" version="6.0">
  <display-name>BackEnd_01_Servlet</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.jsp</welcome-file>
    <welcome-file>default.htm</welcome-file>
  </welcome-file-list>
  <servlet>
    <servlet-name>MyServlet4</servlet-name>
    <servlet-class>com.ssafy.myservlet.MyServlet4</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>MyServlet4</servlet-name>
    <url-pattern>/main</url-pattern>
  </servlet-mapping>
</web-app>
```

위 코드에서 ```<welcome-file-list>```는 우리가 프로젝트를 Run 해서 실행시켰을 때, 기본적으로 실행시킬 페이지를 두는 것이다. 만약 index.html, index.jsp, index.htm 등이 있었다면, 이 프로젝트를 실행시켰을 때 알아서 이것들 중 하나를 잡아서 실행시킬 것이다. (위에서부터 잡아와서 실행을 시킨다.)

그리고 html 파일들은 webapp 폴더 안에 넣는다. 주어진 html 파일들(form_01_get_post.html, form_02_input_text.html, form_03_input.html, form_04_gugu.html, form_05_signup.html, index.html)을 webapp 폴더에 옮겨담는다.

index.html 파일이 있기 때문에, 서버를 Run 하고 보면 다음과 같이 나타난다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/80d15ea7-ac50-4bfd-9232-948ad9ba309d" width="370px" />

<br>

다시 위 web.xml 코드를 보겠다. ```<servlet>``` 태그 안에 위와 같이 servlet-name과 servlet-class를 넣는다. servlet-name은 Servlet4이고, servlet-class에는 Servlet4의 풀 패키지명을 입력한다. 고유해야 하기 때문이다.

이제 Servlet은 등록을 했으니, servlet-mapping을 할 것이다. 이 안에는 servlet-name과 url-pattern의 두 가지를 쓸 수 있다. 이번에는 url-pattern에 ```/main```을 입력한다.

<br>

다시 서블릿을 실행하고, 주소에 ```/main```을 붙이면 다음과 같이 화면에 나타난다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2cfc4bcf-d6ec-49a8-9517-155d70ca8780" width="400px" />

<br>

### Servlet 등록 방법 (Annotation)

Annotation으로 등록하는 방법을 알아보겠다. 이번에는 web.xml을 사용하지 않고 바로 코드에다가 ```@WebServlet("/main2")```를 붙이는 방식으로 간다.

```java
package com.ssafy.myservlet;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/main2")
public class MyServlet5 extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		response.setContentType("text/html;characterset=UTF-8");
		PrintWriter writer = response.getWriter();

		writer.write("""
				<html>
				  <head><title>Hello! SSAFY!</title></head>
				  <body>
				    <h1>Hello! MyServlet5!</h1>
				  </body>
				</html>
				""");
	}
}
```

위와 같이 코드를 수정하고 서버를 Run 한 다음, ```/main2```를 주소에 붙여 작동시키면 다음과 같이 화면에 나타난다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/beb6e06a-21a9-48a3-a575-85fd039172e8" width="400px" />

<br>

이제 등록 방식이 다시 web.xml로 돌아왔다.

<br>

## Servlet 생명 주기 (Life-Cycle)

Servlet 인스턴스는 웹 컨테이너에 의해 제어된다. 서블릿 인스턴스는 ```new Servlet```의 방식으로 만들어진 것이다.

그런데 내가 ```new MyServlet4```, ```new MyServlet5``` 등을 하지 않았다.

이런 것들을 대신 해주는 것이 있는데, 이게 바로 <b>웹 컨테이너</b>이다. 서블릿 컨테이너라는 것은 WAS 안에서 동작을 하는데, 이것이 알아서 ```new Servlet```의 방식으로 만들어주는 것이다.

<br>

Servlet 인스턴스가 존재하지 않으면 다음과 같은 작업을 수행한다.

1. Servlet 클래스 로드
2. Servlet 클래스 인스턴스 생성
3. Servlet 인스턴스 초기화
4. 웹 컨테이너에 의한 서비스 메서드 호출
5. destroy 메서드를 호출하여 Servlet 종료

서비스 메서드는 요청이 들어올 때마다 호출된다.

이때 load, create instance, init, destroy는 한 번씩만 수행되는 반면, service 호출은 여러 번 수행될 수 있다.

즉 <b>웹 서블릿 컨테이너는 서블릿을 싱글턴(Singleton)으로 관리하고 있는 것</b>이다. 딱 한 번만 생성하고 그것을 가져다가 계속 쓰는 것이다.

매번 요청이 올 때마다 서블릿 객체를 만들고 죽이고, 만들고 죽이는 과정을 하다 보면 컴퓨터가 굉장히 힘들어할 수 있다. 그래서 딱 한 번만 만들고 계속 쓰는 것이다.

<br>

Life Cycle을 코드로 보겠다.

```java
package com.ssafy.lifecycle;

import java.io.IOException;

import jakarta.servlet.ServletConfig;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/LifeCycle")
public class LifeCycle extends HttpServlet {
	private static final long serialVersionUID = 1L;
    
	int initCount = 1;
	int doGetCount = 1;
	int destroyCount = 1;
	
    public LifeCycle() {
    	System.out.println("생성자호출");
    }

	public void init(ServletConfig config) throws ServletException {
		System.out.println(initCount++);
		System.out.println("init 호출");
		
	}

	public void destroy() {
		System.out.println(destroyCount++);
		System.out.println("파괴됨");
	}

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		System.out.println(doGetCount++);
		System.out.println("doGet호출");
	}

}
```

어노테이션 방식으로 설정을 하였다. 

위 코드에서 ```int initCount = 1;```, ```int doGetCount = 1;```, ```int destroyCount = 1;```은 인스턴스 변수를 만들어 놓은 것이다. 어차피 인스턴스는 딱 하나밖에 안 쓸 것이기 때문에 그냥 공유해서 쓴 것이다.

<br>

이제 위 코드를 'Run on Server'을 돌려보겠다.

인터넷 브라우저에 "http://localhost:8080/BackEnd_01_Servlet/LifeCycle"를 입력하고 들어가보겠다. 빈 화면이 뜨는데, 만들어놓은 페이지가 없기 때문이다.

이제 콘솔창을 보겠다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/acb51129-ab27-4fb0-8221-5d7f9afe204e" width="700px" />

위와 같이 나온다.

<br>

이제 브라우저에서 새로 고침을 눌러보겠다. 새로 고침 버튼을 두 번 누른 후 다시 콘솔을 보면 다음과 같다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/38bcf230-9229-49fd-b961-ea4b502a0643" width="700px" />

다시 init이 호출되지 않았다. 이미 만들어진 것을 재활용하기 때문이다. doGet만 계속 증가한다.

<br>

이제 Server 창에서 Stop 버튼을 누르고, 다시 Console 창으로 돌아와서 보면 다음과 같이 되어 있다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/be542f55-8945-4d38-a643-3888dc28212c" width="700px" />

destroy가 된 것이다. 내용을 살짝 바꾸고 저장해서 서버가 알아서 껐다 켜지는데, 그때에도 '파괴됨'이 나타날 것이다.

<br>

다시 서버를 껐다 켜도 콘솔에 init이 호출되지 않는다. 왜냐하면 아직 요청이 안 왔기 때문이다. "http://localhost:8080/BackEnd_01_Servlet/LifeCycle" 와 같은 방식으로 요청이 왔을 때 클래스 로더가 읽어서 "어 없어? 읽어. init 해"의 과정이 들어가는 것이다.

<br>

## Servlet 실습

이제 Servlet에 대해 실습을 진행해볼 것이다.

### GET, POST 비교

우선 GET과 POST의 차이점에 대해 알아보겠다.

<table>
	<tr>
		<th>GET</th>
		<th>POST</th>
	</tr>
	<tr>
		<td>지정된 리소스에서 데이터를 요청하는 데 사용</td>
		<td>리소스를 생성/업데이트하기 위해 서버에 데이터를 보내는 데 사용</td>
	</tr>
	<tr>
		<td>query string(name/value 쌍)이 URL에 포함되어 전송됨.<br>POST와 비교하여 보안에 취약함</td>
		<td>HTTP header의 body에 파라미터를 포함하여 전송<br>데이터 길이에 대한 제한 없음<br>매개변수가 브라우저나 웹 서버에 저장되지 않음</td>
	</tr>
	<tr>
		<td>URL이 길이 제한이 있으므로, 전송 가능한 데이터의 길이가 제한적<br>(URL maximum characters : 2048)<br>ASCII 문자만 가능</td>
		<td>제한 없음. 바이너리 데이터도 허용</td>
	</tr>
</table>

<br>

query string이란 어떠한 요청이 있을 때 그 뒤에 ? 가 붙고 그 뒤에 쭉 날라가는 것이다. 이것이 key - value의 형태로 이루어져 있다. query string이 여러 개라면 & 로 연결을 한다.

그런데 query string은 말 그대로 URL 주소에 붙어져서 들어가기 때문에, URL 공간에 넘길 수 있는 데이터의 한계가 있다. 그래서 길이가 제한적이다.

<br>

POST는 서버에 데이터를 보내는 데 사용하기 때문에, URL에 데이터를 붙이지 않고 HTTP header의 body에 데이터를 실어서 보낸다. 그래서 길이는 제한이 없다.

<br>

보통 'GET은 눈에 보여도 괜찮은 것, POST는 눈에 보이면 안 되는 것'이라고 구분하기도 하지만, 사실 컴퓨터를 조금만 알면 개발자 도구 창을 통해 얻어낼 수 있으니 그것 또한 약간의 보완이 필요하다.

<br>

### URL 구성요소

URL의 구성 요소에 대해 알아보겠다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/9de20600-0bb7-4f2c-ad40-a50ae138d1a6" width="400px" />

<table>
	<tr>
		<th>구성요소</th>
		<th>설명</th>
	</tr>
	<tr>
		<th>프로토콜</th>
		<td>절차를 포함한 통신규약</td>
	</tr>
	<tr>
		<th>서버</th>
		<td>웹 페이지를 요청할 서버의 주소, 실제 IP 주소나 도메인을 입력할 수 있다.</td>
	</tr>
	<tr>
		<th>경로</th>
		<td>서버 내의 상세 경로</td>
	</tr>
	<tr>
		<th>쿼리 스트링</th>
		<td>추가로 서버로 데이터를 전송하기 위해서 사용된다.<br>'?' 마크를 적어 시작을 표시한다. parameter_name=value 형태로 작성하며, 파라미터가 여러 개일 경우 '&'로 구분하여 작성한다.</td>
	</tr>
</table>

<br>

프로토콜은 약속이다. 이 약속은 굉장히 많은 종류가 있다. 파일 전송을 처리하는 FTP(File Transfer Protocol), HTTP 등 여러 가지가 있다.

서버에 실제로는 IP 주소를 작성해야 하는데, 그 IP 주소를 외우기는 매우 어렵다. 그래서 그 IP 주소를 대신할 수 있는 이름 같은 것을 등록해두고 쓰는 것이다. 이것을 '도메인'이라고 하고, 등록을 하기 위해서는 금액을 지불해야 한다. 

내가 기본적으로 내 컴퓨터에 갈려면 ```127.0.0.1```을 입력하면 된다. 그러면 본인 컴퓨터의 주소로 들어갈 수 있는 것이다. 실질적으로 웹 상에서 바라보는 내 컴퓨터의 주소는 다르겠지만, 내가 내 컴퓨터의 주소를 가지고 무언가를 하려 한다면 ```127.0.0.1```을 입력하고 들어가면 된다.

이것 말고 내가 localhost라는 이름으로 쓰기로 했으니, Run on Server을 했을 때 브라우저에 localhost라고 나타나는 것이다.

그리고 이 뒤에는 8080과 같이 번호가 붙는데, 이는 포트 번호이다. 포트 번호는 HTTP를 위한 문이라고 생각하면 된다. '8080'이라는 문을 열겠다는 것이다. 8080은 Tomcat에서 사용하고 있는 포트 번호이다. DB는 '3306'이라는 포트 번호를 가지고 있다. 그래서 '127.0.0.1:3306'이라고 하면 이는 내 컴퓨터에서 돌고 있는 MySQL에 접속하겠다는 의미이다. 포트 번호는 여러 개를 쓸 수 있지만, 기본적으로 사용하는 포트 번호가 있으니 건드리지 않는 것이 좋다. http의 기본 포트번호는 80이고, https의 기본 포트번호는 443이다.

<br>

### input tag 실습

* text (영어/한글)
* number
* radio
* checkbox
* select
* textarea
* hidden

<br>

GET, POST를 처리할 수 있는 html 파일을 하나 생성한다.

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>GET/POST</title>
</head>
<body>
	<h2>GET Form</h2>
	<form action="" method="GET">
		<input type="text" name="id">
		<input type="submit">
		<button>보내</button>
	</form>
	<h2>POST Form</h2>
	<form action="" method="POST">
		<input type="text" name="id"> 
		<input type="submit">
	</form>
</body>
</html>
```

그리고 해당 프로젝트를 Run on Server로 실행시킨다. 실행시킨 후 웹 브라우저에서 'form_01_get_post.html' 버튼을 클릭하고 들어간다. 그러면 주소가 ```http://localhost:8080/BackEnd_01_Servlet/form_01_get_post.html```가 되어 있을 것이다.

GET From의 input 안에 "John"이라고 입력하고 '제출' 버튼을 클릭하면, URL이 ```http://localhost:8080/BackEnd_01_Servlet/form_01_get_post.html?id=John```로 바뀐다.

해당 요청의 뒤에 새롭게 붙이는 것이다.

POST Form의 input 안에 "John"이라고 입력하고 '제출' 버튼을 클릭하면 무언가 요청이 갔지만 쿼리 스트링에 보이지는 않는다.

개발자 도구에 들어가서 Network 탭에 들어간 다음, 다시 POST Form의 input 안에 "John"을 입력하고 '제출' 버튼을 클릭하면 다음과 같은 화면이 나타난다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/a8fbe55b-05c5-4e7f-a3ae-1be03a769a62" width="750px" />

그리고 여기서 Payload에 들어가보면 다음과 같이 나온다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/de5f60dd-2ca6-428f-8587-9332e3e27c1e" width="400px" />

우리 눈에 보이지는 않지만, 실질적으로는 이런 식으로 서버에 날아간다는 것이다.

<br>

이번에는 다른 html 파일을 보겠다.

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>input_text</title>
</head>
<body>
	<h2>GET Form</h2>
	<form action="ServletParameter" method="GET">
		<input type="text" name="text"> 
		<input type="submit">
	</form>

	<h2>POST Form</h2>
	<form action="ServletParameter" method="POST">
		<input type="text" name="text"> 
		<input type="submit">
	</form>
</body>
</html>
```

이제는 ServletParameter라는 곳에 요청을 보내고 싶다. 내가 action에 아무 것도 안 쓰면 내 요청을 그대로 보낸다고 하는 것인데, 그렇게 하지 않을 것이다. "ServletParameter"라고 하는 곳에 보내겠다는 것이다.

<br>

이제 ServletParamter 클래스를 생성한다.

```java
package com.ssafy.practice;

import java.io.IOException;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/ServletParameter")
public class ServletParameter extends HttpServlet {
	private static final long serialVersionUID = 1L;
    
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		String text = req.getParameter("text");
		
		System.out.println(text);
	}
	
	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
	}
}
```

위의 input_text html 파일을 보면, ```name="text"```로 날아오기로 했다. 그리고 GET 요청과 POST 요청이 있다.

그러면 내가 GET 요청을 위해서는 위 ServletParameter 클래스에 doGet메서드를 쓸 수 있고, POST 요청을 위해서는 doPost메서드를 써서 정의한다.

```req``` 안에 ```getParameter()``` 메서드가 있다. 그리고 ```String text = req.getParameter("text");```의 방식으로 기입한다. 여기 있는 "text"는 input으로 날아온 text이다. 이렇게 String text 변수에 담은 다음, 이것을 콘솔에 출력해보겠다.

그리고 실행된 웹 브라우저에서 'form_02_input_text.html' 버튼을 클릭해 ```http://localhost:8080/BackEnd_01_Servlet/form_02_input_text.html```로 이동한 다음, 'GET Form'의 input에 "hello"라고 입력하고 제출 버튼을 클릭한다.

그러면 response에 아무 것도 안 만들어 놨으니 브라우저에서는 아무것도 안 뜬다. 그런데 콘솔창을 보면 다음과 같이 출력되어 있다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b5253627-d530-4952-a158-01755c870a26" width="700px">

요청 받는 파라미터를 가지고 와서 콘솔에 띄운 것이다.

POST도 마찬가지 방법으로 처리할 수 있다. 

<br>

이번에는 새로운 html 파일을 보겠다.

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h2>GET Form</h2>
	<form action="ServletParameter2" method="GET">
		
		<h3>Text</h3>
		<label for="text">텍스트 입력</label>
		<input type="text" name="text" id="text"> <br>
		
		<h3>Number</h3>
		<label for="number">숫자 입력</label>
		<input type="number" name="number" id="number"> <br>
		
		<h3>Radio 버튼</h3>
		<input type="radio" name="campus" id="su">
		<label for="su">서울</label><br>
		<input type="radio" name="campus" id="dj">
		<label for="dj">대전</label><br>
		<input type="radio" name="campus" id="gj">
		<label for="gj">광주</label><br>
		<input type="radio" name="campus" id="gm">
		<label for="gm">구미</label><br>
		<input type="radio" name="campus" id="buk">
		<label for="buk">부울경</label><br>
		<br>
		
		<h3>Checkbox</h3>
		<input type="checkbox" name="fruit" id="apple" value="apple">
		<label for="apple">사과</label><br>
		<input type="checkbox" name="fruit" id="pear" value="pear">
		<label for="pear">배</label><br>
		<input type="checkbox" name="fruit" id="banana" value="banana">
		<label for="banana">바나나</label><br>
		<input type="checkbox" name="fruit" id="orange" value="orange">
		<label for="orange">오렌지</label><br>
		<br>
		
		<h3>Select</h3>
		<select name="programming-language">
			<option value="C">씨언어</option>
			<option value="C++">씨플플</option>
			<option value="Java">자바</option>
			<option value="Python">파이썬</option>
		</select>
		<br>
		
		<h3>TextArea</h3>
		<textarea rows="10" cols="10" name="content"></textarea>
		<br>
		
		<h3>Hidden</h3>
		<input type="hidden" name="action" value="hi">
		<input type="submit">
	</form>
</body>
</html>
```

위 코드에서는 ServletParameter2가 있으니, ServletParameter2 클래스를 만들겠다.

```java
package com.ssafy.practice;

import java.io.IOException;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/ServletParameter2")
public class ServletParameter2 extends HttpServlet {
	private static final long serialVersionUID = 1L;
    
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		//text / number / checkbox .... 받아서 처리해볼수있다
		
		int num = Integer.parseInt(req.getParameter("number"));
	}
}
```

위 html 파일을 보면, GET 방식으로 text도 넘겨보고, number도 보내보고, select도 보내보고, checkbox도 보내는 등 다양한 데이터들을 쭉 보낸다.

그래서 ServletParameter2 클래스에 직접 처리할 수 있다. 저기서 보낸 text, number, checkbox 등을 받아서 처리해볼 수 있는 것이다.

```req.getParameter("~~~")```의 반환 타입은 문자열이다. 그래서 input 태그에서 넘겨받은 데이터를 ```Integer.parseInt()```로 변환해야 한다.

<br>

### Servlet 요청과 응답 (기존 방식)

Servlet 요청과 응답에서 기존 방식은 매핑 주소마다 servlet이 존재했다. 그러다 보니 많은 서블릿이 필요했다.

가령 게시글을 작성하기 위해 ```/write```라는 매핑이 들어왔을 때 Servlet1, 게시글을 수정하기 위해 ```/update```라는 매핑이 들어왔을 때 Servlet2, 게시글 조회를 하기 위해 Servlet3, 게시글을 삭제하기 위해 Servlet4 ... 의 방식이었던 것이다.

그런데 요즘에는 이렇게 Servlet을 여러 개 만드는 것이 아니라, Servlet을 딱 하나만 만들고 그것으로 하여금 기능들을 호출하도록 하였다. 이게 <u>MVC 패턴의 출발</u>이다.

<br>

### Servlet 요청과 응답 (Front Controller)

웹에서 발생하는 모든 요청에 대해 호출되는 Servlet을 만들어 처리함.

Servlet을 딱 하나만 만들고, 그 안에서 필요에 따라 게시글 등록, 수정, 조회, 삭제를 넣는 것이다.

그러면 요청이 그냥 ```/board```의 방식으로 올 것인데, 그것에 따라 게시글 등록, 수정, 조회, 삭제를 어떻게 구분할 수 있을까?

사용자는 모르게 "이것은 어떠한 요청이다"라는 것을 실어서 보내줘야 한다.

<br>

사용자가 모르게 하기 위해 다음과 같이 한다. 

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0921700b-86dd-4cf1-8233-0809d0670300" width="650px">

구구단을 구할 때 사용하는 html 코드이다. 위 코드를 보면, ```type="hidden"```이라고 되어 있다. 

<br>

또 다른 코드를 보자.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/54552566-19e0-44c2-a4fc-e5905a513c97" width="650px">

signup.html의 코드로, type은 hidden으로 입력되어 있다.

<br>

이제 MainServlet 자바 클래스를 하나 만든다. 

```java
package com.ssafy.practice;

import java.io.IOException;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/MainServlet")
public class MainServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
    
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		doProcess(req, resp);
	}
	

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		doGet(req, resp);
	}
	

	private void doProcess(HttpServletRequest req, HttpServletResponse resp) {
		String action = req.getParameter("action");
		
		switch (action) {
		case "gugu": {
			// 구구단 처리해서 반환해
		}
		case "regist": {
			// 등록 처리해
		}
		default:
		}
	}
}
```

```/MainServlet```이라는 요청이 있다고 했을 때, doPost의 경우 doGet을 다시 한 번 호출했다. 그리고 doGet에서 doProcess()라는 메서드를 만들 것이다. (이때 doProcess는 만들어져 있는 것이 아니고 내가 만들려고 쓴 것이다.)

그리고 아래에 doProcess() 메서드를 만든다. 그리고 action 변수에 ```req.getParameter("action")```를 담는다. 그리고 switch문으로 case가 "gugu"면 구구단 처리해서 반환하게 하고, "regist"면 등록 처리를 하게 하는 것이다.