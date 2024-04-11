---
layout: post
title: JSP 기본 객체 영역
categories: Spring-SpringBoot
date: 2024-04-11 16:11:00 +0900
---
JSP의 기본 객체에 대해 알아보자.

JSP에는 여러 가지 기본 객체가 있었지만, 크게 4개의 영역을 가지고 있다. 그 크기에 맞춰서 Page 영역 (pageContext), Request 영역 (request), Session 영역 (session), Application 영역 (application)으로 되어 있다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b73637f2-1c7d-4840-8cb1-72fa3539b025" width="450px" />

* <b>Page 영역</b> : 하나의 페이지 정보를 담고 있는 영역. 페이지가 바뀌면 새로운 객체가 생성됨.
* <b>Reqeust 영역</b> : 하나의 요청을 처리할 때 사용되는 영역. 응답이 완료되면 사라짐.
* <b>Session 영역</b> : 하나의 웹 브라우저와 관련된 영역. 로그인 정보 등을 저장.
* <b>Application 영역</b> : 웹 어플리케이션 영역. 어플리케이션이 시작되면 종료될 때까지 유지.

<br>

## JSP 기본 객체 영역 (Scope) 메서드

servlet과 페이지 간 정보를 공유하기 위해서 메서드를 지원하다. 아래는 각 영역에서 사용할 수 있는 공통 메서드이다.

<table>
    <tr>
        <th>메서드</th>
        <th>반환형</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>setAttribute(String name, Object value)</td>
        <td>void</td>
        <td>key-value 형태로 각 영역에 데이터를 저장.<br>name이 value를 얻어오기 위한 key가 된다.</td>
    </tr>
    <tr>
        <td>getAttribute(String name)</td>
        <td>Object</td>
        <td>현재 객체에서 인자로 받은 이름으로 설정된 값을 반환</td>
    </tr>
    <tr>
        <td>getAttributeNames()</td>
        <td>Enumeration</td>
        <td>현재 객체에서 설정된 값의 모든 속성의 이름을 반환</td>
    </tr>
    <tr>
        <td>removeAttribute(String name)</td>
        <td>void</td>
        <td>현재 객체에서 인자로 받은 이름으로 설정된 값을 삭제</td>
    </tr>
</table>

<br>

<hr>

<br>

이제 코드를 통해 알아보겠다.

우선 03_FirstScope.jsp 파일의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JSP 기본객체</title>
</head>
<body>
	<%
		pageContext.setAttribute("name", "page kim");
		request.setAttribute("name", "request kim");
		session.setAttribute("name", "session kim");
		application.setAttribute("name", "application kim");
		
		System.out.println("A.jsp");
		System.out.println("페이지 속성 " + pageContext.getAttribute("name"));
		System.out.println("요청 속성 "+ request.getAttribute("name"));
		System.out.println("세션 속성 "+ session.getAttribute("name"));
		System.out.println("어플리케이션 속성 "+ application.getAttribute("name"));
		
		request.getRequestDispatcher("04_SecondScope.jsp").forward(request, response);
		
	%>
	
</body>
</html>
```

위 코드를 보면, pageContext, request, session, application 영역 각각의 "name" 위와 같이 내용을 저장했다. 그리고 콘솔에 한 번 찍어보기 위해 ```System.out.println()```을 하였고, 마지막에 포워딩(forwarding)을 진행하였다.

<br>

다시 Run on Server을 누르고, 이번에는 화면에서 03_FirstScope.jsp 를 클릭한다. 그러면 다음과 같은 화면이 나타난다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/6f8c3162-4c15-4166-bfe6-3a1e5b7aa05e" width="450px" />

지금 내가 보고 있는 이 페이지는 02_SecondScope.jsp 이다.

<br>

02_SecondScope.jsp 파일의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JSP 기본객체</title>
</head>
<body>
	<p>페이지 속성 : <%= pageContext.getAttribute("name") %></p>
	<p>요청 속성 : <%= request.getAttribute("name") %></p>
	<p>세션 속성 : <%= session.getAttribute("name") %></p>
	<p>애플리케이션 속성 : <%= application.getAttribute("name") %></p>
	
	<a href="05_ThirdScope.jsp">세번째 페이지</a>
</body>
</html>
```

<br>

지금 내가 보고 있는 이 페이지는 02_SecondScope.jsp 이다. 그런데 위에 URL을 보면 ```03_FirstScope.jsp```라고 되어 있다. 어떻게 이것이 Second 인 것일까?

콘솔창에 로그를 보면 다음과 같다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/6d116e36-0139-41d5-b20b-1e992f3dbd14" width="700px">

포워딩을 바로 했기 때문에 주소가 바뀌지 않은 채로 이곳으로 넘어왔더니, 페이지 속성만 null 이라고 한다.

페이지 영역은 각 페이지마다 생성되는 고유한 영역이다. 그래서 03_FirstScope.jsp 페이지에서 있던 페이지 속성이 02_SecondScope.jsp 까지 넘어오지 않은 것이다. 왜냐하면 새롭게 만들어진 것이기 때문이다.

First 에서 Second 로 포워딩(forwarding)을 했다. 즉, 그 요청을 가지고 그대로 넘겨져 왔으니 request가 사라져 있는 것이다.

세션이라고 하는 것은 웹 브라우저와 관련된 것이니, 브라우저를 전부 다 끄지 않으면 세션은 유지된다. 

그리고 application이라고 하는 것은 나의 WAS에 이 값을 저장하는 것이다.