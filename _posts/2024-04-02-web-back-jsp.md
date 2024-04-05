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

## JSP 기본태그

이제 본격적으로 JSP의 내용에 대해 다룰 것이다.

JSP의 기본 태그는 크게 5가지가 있다.

<table>
    <tr>
        <th>종류</th>
        <th>사용용도</th>
        <th>형식</th>
    </tr>
    <tr>
        <td>스크립트릿(scriptlet)</td>
        <td>자바코드 작성</td>
        <td>&#60;&#37;  &#37;&#62;</td>
    </tr>
    <tr>
        <td>선언(declaration)</td>
        <td>변수와 메서드를 선언</td>
        <td>&#60;&#37;! &#37;&#62;</td>
    </tr>
    <tr>
        <td>표현식(expression)</td>
        <td>계산식이나 함수를 호출한 결과를 문자열 형태로 출력</td>
        <td>&#60;&#37;= &#37;&#62;</td>
    </tr>
    <tr>
        <td>주석(comment)</td>
        <td>JSP 페이지 설명 작성</td>
        <td>&#60;&#37;-- --&#37;&#62;</td>
    </tr>
    <tr>
        <td>지시자(directive)</td>
        <td>JSP 페이지 속성 지정</td>
        <td>&#60;&#37;&#64; &#37;&#62;</td>
    </tr>
</table>

<br>

HTML의 주석은 &#60;!-- --&#62;인데, JSP의 주석과 다른 것을 알 수 있다. 

<br>

### 스크립트릿 (Scriptlet)

스크립트릿은 스크립팅 언어(java)로 작성된 코드 조각을 포함하는 데 사용된다. 

```jsp
<%
    // 자바코드 작성
%>
```

그러면 스크립트릿을 예시 코드로 알아보자.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>스크립트릿</title>
</head>
<body>
	<h2>스크립트릿 연습</h2>
	<%
	int A = 10;
	int B = 20;

	int sum = A + B;
	
	out.println(A + "+" + B + "=" + sum);	
	out.println(A + "+" + B + "=" + sum);	
	
	%>
	<a href="index.html">홈으로</a>
</body>
</html>
```

우선 위 코드에서, ```<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>```를 보면, language는 자바, contentType은 text로 넘어왔을 때 html로 인쇄했으면 좋겠다는 것, charset은 UTF-8 방식이면 좋을 것 같고, pageEncoding은 UTF-8로 하라는 것이다. 

이때 language의 경우 안 써도 default가 Java인데, 쓸 수 있는 언어도 Java밖에 없다. 그래도 이렇게 자바라고 명시한 이유는, 나중에 발전할 수도 있기 때문이다.

위 코드에서 스크립트릿 안쪽을 보면 변수 A, B가 선언되어 있고, 그것을 더한 30이 sum 변수에 들어간 것을 알 수 있다. 

30을 출력하기 위해서는 어떻게 해야 할까? ```System.out.println()```의 경우 콘솔창에 출력하는 것이니 당장 쓸 것은 아니다. 출력을 위해 기본적으로 가지고 있는 객체가 있는데, ```out```이다. 그래서 ```out.print(sum)```의 방식으로 화면에 띄울 것이다. (out.print(), out.println() 둘 다 딱히 줄바꿈 되지 않으니 아무 거나 쓰면 된다.)

이제 Run on Server을 하고 브라우저를 보면 다음과 같이 나온다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/372b97e9-b025-404e-90c4-73c4456af68e" width="450px" />

<br>

그런데, 화면에 출력되는 것들을 줄바꿈 하고 싶다면 어떻게 해야 할까?

다음 예시 코드를 보자.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>구구단 출력</title>
</head>
<body>
	<h2>구구단</h2>
	<%
	for (int i = 2; i < 10; i++) {
		for (int j = 1; j <= 9; j++) {
			out.print(i + " * " + j + " = " + (i * j));
			out.print("<br>");
		}
	}
	%>
	<a href="index.html">홈으로</a>
</body>
</html>
```

위 코드에 대해 Run on Server를 하면 출력 결과는 다음과 같다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5a00dbac-ffb0-4b9a-8713-22e94befc10e" width="450px" />

<br>

### 선언부 (Declaration)

다음은 JSP 기본 태그 중 선언부(Declaration)이다. 선언부는 멤버변수의 선언이나 메서드를 선언하는 영역이다.

```jsp
<%!
    // 변수 선언
    // 메서드 선언
%>
```

예시 코드를 통해 알아보자.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%! int A = 10;
	int B = -20;

	String name = "SSAFY";

	public int add(int A, int B) {
		return A + B;
	}

	public int abs(int A) {
		return A > 0 ? A : -A;
	} %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>선언부</title>
</head>
<body>
	<%
	int C = 10;
	// 여기에 메서드를 정의할 수는 없을까?
	/* 	public int mul(int A, int B){
		return A*B;
	} */
	out.print(add(A, B));
	out.print("<br>");

	out.print(abs(B));
	out.print("<br>");
	%>
	<a href="index.html">홈으로</a>
</body>
</html>
```

이제 Run on Server을 하고 화면에서 보면 다음과 같다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/9d1fbe65-f0bf-4c4a-b246-f7222d0324d2" width="450px" />

<br>

스크립트릿 안에 메서드를 정의할 수는 없을까? <b>안 된다.</b> 스크립트릿 안에 작성한 내용은 service() 메서드 안에 정의가 된다. <u>자바에서는 메서드 안에 메서드를 선언하는 것이 허용되지 않는다. 그래서 스크립트릿 안에서 쓸 수는 없고, 선언부에 작성해야 하는 것</u>이다.

<br>

선언부에 대해 좀 더 알아보겠다.

예시 코드를 보자.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>

<%!int count1 = 0;%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>호출</title>
</head>
<body>
	<% 
		int count2 = 0;
	
	out.print("count1 : " + (count1++) + "<br>");
	out.print("count2 : " + (count2++) + "<br>");
	%>

	<a href="index.html">홈으로</a>
</body>
</html>
```

위 코드를 보면, 선언부에 count1 변수를 선언 및 0으로 초기화해두고, 스크립트릿 안에 count2 변수를 선언 및 0으로 초기화 하였다.

그리고 Run on Server을 한 다음 화면에 출력된 것을 보면 다음과 같다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b7818e28-e821-411c-a916-6798c24ff3c3" width="450px" />

현재 둘 다 0으로 되어 있다.

그런데 새로고침을 하면 어떻게 될까? 새로고침을 한 번 하면 다음과 같이 화면에 나타난다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/72746c63-5a6b-4b4f-ba4b-3c52584217ff" width="450px" />

새로고침 버튼을 계속 누르면 count1의 값만 계속 올라가고 count2의 값은 0으로 남아있다.

새로고침을 눌렀더니 전역변수인지 아닌지의 차이에 따라 값의 변화가 달라지는 것이다. 선언부에 있는 count1는 멤버변수이고, 스크립트릿에 있는 count2는 메서드 안에서 동작하는 지역변수이다.

클라이언트가 서버에 "05_count.jsp 주세요" 하고 요청을 했다면, 처음에 요청이 날아가는 순간 WAS에서 동작하는 서블릿 컨테이너라고 하는 것이 "어 지금 이 서블릿 있던가?"라고 한다. 그러면 "아 이거 jsp네." 하면서 서블릿으로 바꾼다. 이 서블릿이 하나의 클래스 파일이고, 서블릿 컨테이너는 클래스를 로드하고 인스턴스를 생성한다. 그리고 init() 메서드를 동작시킨다.

인스턴스가 생성되었으니 count1은 0으로 초기화 되어 있고, 이 안에서 GET 요청으로 service()를 호출했으니 서비스 내부에 있던 count2가 0으로 초기화 된다.

그래서 서버를 끄지 않고 새로고침할 때마다 service()는 매번 다시 호출된다. 그러니 count2는 매번 0으로 출력되는 것이다.

내가 서버를 죽이기 전까지는 05_count.jsp 는 다시 만들지 않는다. 그냥 service() 만 계속 호출할 뿐이다. 그래서 값이 누적해서 올라간다. <b>싱글턴(singleton)으로 만들어져 있기 때문</b>이다.

<br>

### 표현식 (Expression)

표현식은 변수의 값이나 계산식 혹은 함수를 호출한 결과를 문자열 형태로 웹 문서에 출력하게 해준다.

예시 코드를 보자.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%! int A = 10;
	int B = -20;

	String name = "John";

	public int add(int A, int B) {
		return A + B;
	}

	public int abs(int A) {
		return A > 0 ? A : -A;
	} %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>표현식</title>
</head>
<body>
	<%= name %><br>
	<%= A + B %><br>
	<%= add(A, B) %><br>

	<a href="index.html">홈으로</a>
</body>
</html>
```

Run on Server을 하여 화면을 보면 다음과 같이 출력된다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ca7411ea-3400-4da5-bda9-dac09882eceb" width="450px" />

이번에는 &#60;&#37;= &#37;&#62;를 이용한다. 선언부에 선언 및 초기화한 A, B 그리고 add() 메서드를 이용해서 바로 화면에 출력하는 것이다.

아까 out.print()를 쓰는 것보다 조금 더 간결하고 좋은 것 같은데, 차이점이 있다. 자바 코드인데 표현식 안에는 세미콜론(;)이 안 들어가는 것이다. 가령 내가 ```<%= name %>```로 작성했지만, 컴파일러는 ```out.print(name);```으로 변환해버린다. name 인자가 print 메서드 안으로 들어가는 것이다. 그래서 만약 내가 표현식을 사용할 때, ```<%= name; %>```의 방식으로 입력하면 에러가 발생한다.

<br>

### 주석문 (Comment)

주석문은 작성한 코드에 대한 설명을 기술할 경우에 사용한다. HTML 주석문과 동일한 기능을 하지만, HTML 주석문은 클라이언트에 보여지고, JSP 주석문은 보여지지 않는다는 차이가 있다.

예시 코드를 보자.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>주석문</title>
</head>
<body>
	<!-- 이것은 HTML 주석입니다. (클라이언트에게 노출이 됩니다.) -->
	<%-- 이것은 JSP 주석입니다.  (클라이언트에게 노출이 되지 않습니다.) --%>
	<%
	    //이것은 클라이언트에게 노출이 될까요?
	%>
	<a href="index.html">홈으로</a>
</body>
</html>
```

Run on Server을 하고 화면에서 개발자 도구를 켠 다음, Elements를 보면 다음과 같다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4abc7f9f-4455-4bb4-9d9a-a3adb0f91c2b" width="850px" />

보면 노출되는 것은 HTML 주석밖에 없다. JSP 주석과 스크립틀릿으로 작성한 자바 주석은 클라이언트에게까지 가지 않는 것이다.

특히 JSP 주석 &#60;&#37;-- --&#37;&#62;은 서블릿(Servlet)으로 변환하는 단계에까지 가지도 못한다. 컴파일러에 의해 클래스(.class) 파일로 바뀔 때 가지 않는다.

그래서 HTML 주석으로 정보를 남겨놓으면 굉장히 위험하다.

<br>

### 지시자 (Directive)

지시자는 웹 컨테이너(Tomcat)가 JSP를 번역하고 실행하는 방법들을 서술한다.

지시자에는 page, include, taglib 세 가지가 있다.

* <b>page</b> : 해당 JSP 페이지 전반적으로 환경을 설정할 내용 저장
* <b>include</b> : 현재 페이지에 다른 파일의 내용 삽입할 때 사용
* <b>taglib</b> : 태그 라이브러리에서 태그를 사용할 수 있는 기능 제공

include 지시자는 JSP 파일 안에 또 다른 JSP를 넣고 싶을 때 이용할 수 있다.

지시자를 사용할 때에는 &#60;&#37;&#64; &#37;&#62;를 사용한다.

```jsp
<%@ 지시자 속성 = "값" %>
```