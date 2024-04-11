---
layout: post
title: Cookie
categories: Spring-SpringBoot
date: 2024-04-09 16:17:00 +0900
---
Cookie에 대해서 알아보겠다.

Cookie는 언제 사용하는 것일까? 가령 내가 쿠팡 사이트에 들어가서 삼성 갤럭시 폴드를 하나 장바구니에 담았다고 하자.

나는 지금 쿠팡 사이트에 로그인을 하지 않았다. 그런데 장바구니에 내가 담은 물건이 들어 있다. 로그인을 하지 않았는데 쿠팡 서버에서는 도대체 나의 어떤 것을 보고 내가 지금 장바구니에 이러한 물건을 저장했는지를 알 수 있을까?

<br>

내가 장바구니에 물건을 담는 순간, 쿠팡 서버에서 쿠키(Cookie)를 하나 만든다. 이 쿠키 안에는 물건이 담겨있다는 정보가 담겨 있고, 서버는 이 쿠키를 나에게 던져준다. 그리고 나는 이 쿠키를 받아서 가지고 있다.

이제 내가 "장바구니 보고 싶어" 하고 ```/cartView``` 요청을 날리면 "지금 나 이런 거 가지고 있으니까, 같이 넘겨서 이런 정보도 같이 보여줘"가 된다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4fc2eebb-36bd-462d-85b6-9bfcaa259163" width="600px" />

쿠키는 개발자 도구창을 열면 볼 수 있다. <kbd>F12</kbd>를 누르고 개발자 도구 창을 켠다. 그리고 Application 탭에 들어가서 Cookies를 클릭하고, ```https://cart.coupang.com```을 클릭하면 연결되어 있는 무수히 많은 쿠키들이 쭉 보인다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/62fcf020-e7df-4c97-8bca-d92a2669fcaa" width="1000px" />

이 중에서 음영 처리되어 있는 'sid'라는 쿠키가 내가 장바구니에 넣은 갤럭시 폴드에 대한 쿠키이다. 이제 이 쿠키를 삭제하고 새로고침하면 다음과 같이 된다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/a9bdd884-85b9-4ec5-8781-dd1ad4441ead" width="1000px" />

장바구니에 물건이 없어진 것을 볼 수 있다.

아까 갤럭시 폴드를 넣었던 쿠키를 지워버렸기 때문에 더 이상 이 쿠키를 쿠팡 사이트에 전송한다 한들, 서버에서는 "빈 장바구니 줄게" 가 되는 것이다.

매번 데이터베이스에 우리가 "어떤 사람이 장바구니에 무엇을 넣어놨어요" 하고 저장을 하면 데이터베이스에서도 그 정보를 확보하고 있으니 이는 자원 낭비이다. 이런 간단한 정보들 같은 경우 쿠키로 처리한다.

<br>

다시 장바구니에 담아보겠다. 따로 로그인을 하지 않은 상황이다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/3eb29a97-d9e0-490e-8d39-011039c0907f" width="1000px" />

그러면 다시 위 그림과 같이 쿠키가 생성된다.

이제 시크릿 탭을 켜보겠다. 시크릿 탭을 켜는 것은 새로운 브라우저를 켠 것이다. 크롬에서 일반 탭과 시크릿 탭은 서로 다른 브라우저로 인식을 한다.

이번에는 시크릿 탭에서 쿠팡을 검색하고 들어가 장바구니를 보자.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/fa420213-cee9-4e75-94fe-bd538445777c" width="1000px" />

장바구니에 없다. 즉, <b>쿠키라는 것은 브라우저 별로 관리를 하는 것</b>이다. <u>각 브라우저 별로 다른 클라이언트로 생각을 하겠다는 것이다.</u>

<br>

## Cookie

Cookie라는 것은 웹 서버가 클라이언트의 웹 브라우저에 저장하는 작은 데이터 조각이다.

필요에 따라 요청(request) 시 서버로 같이 전송된다. 무조건적으로 내가 가지고 있는 모든 쿠키들이 어떤 요청을 보낼 때마다 한 방에 다 가는 것이 아니라, 필요 시에 이러이러한 쿠키들을 보내겠다는 것이다.

쿠키는 Key : Value 형태의 문자열 데이터이다.

그리고 웹 브라우저(클라이언트) 별로 별도의 쿠키를 생성한다. 브라우저는 하나의 컴퓨터이다. 브라우저가 다르다면 다른 사용자인 것이다.

(공식 문서: <a href="https://jakarta.ee/specifications/platform/8/apidocs/javax/servlet/http/cookie">https://jakarta.ee/specifications/platform/8/apidocs/javax/servlet/http/cookie</a>)

<br>

## Cookie 사용 목적

Cookie의 사용 목적은 다음과 같다.

* 세션 관리 (사용자 아이디, 장바구니 등)를 위해 사용
* 사용자가 설정한 환경 등을 기억하여 페이지 제공
* 사용자의 행동과 패턴을 분석
* 사용자의 관심에 따른 광고를 타겟팅 하기 위해서 사용

가령 내가 네이버 부동산에 들어가서 이것저것 검색 내역을 설정해 두었을 때, 나중에 브라우저를 새로 켜고 들어가도 "역삼에 얼마짜리 아파트 ~" 등의 정보가 저장되어 있는 것이다. 내가 설정한 환경 등을 기억하여 페이지를 제공하는 차원에서 쿠키가 사용된다.

내가 어떤 사이트에 들어갔을 때 'Accept all Cookies'를 클릭하여 승인하면 내가 그 사이트에 어떤 것을 클릭하는지, 검색하는지 등등을 사이트가 가지고 분석을 한다. 그래서 내 관심에 맞는 광고를 뿌려준다.

<br>

## Cookie 동작 순서

* Client가 요청 생성
* WAS는 Cookie를 생성하고 HTTP Header에 Cookie를 넣어 응답
* Client(Browser)는 Cookie를 저장, 해당 서버에 요청할 때 요청과 함께 Cookie를 전송
* Cookie는 브라우저가 종료되더라도 계속 저장되기 때문에(만료 기간 전까지) 동일 사이트 재 방문하여 요청 시 필요에 따라 Cookie가 재 전송됨

<br>

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f0cf87d7-50e0-4706-87d4-06424ed156c7" width="650px" />

<br>

우선 클라이언트가 요청을 생성하면, WAS는 Cookie를 생성하고 HTTP Header에 Cookie를 넣어서 응답한다. 이전에 알아본 예시를 반영한다면, 갤럭시 폴드를 저장한 쿠키를 응답 헤더에 실어서 사용자에게 저장을 시키는 것이다.

그러면 클라이언트는 쿠키를 저장하고, 해당 서버에 요청할 때 쿠키를 요청과 함께 보낸다. 가령 "장바구니를 보여줘"라고 요청을 할 때, 이 쿠키를 같이 실어서 보내는 것이다. 이 쿠키는 이 안에 "갤럭시 폴드 내가 담고 있음"이라고 정보를 가지고 있는데, 이것을 서버에게 돌려준다. 그러면 서버는 "오 너 갤럭시 폴드 가지고 있구나"의 정보를 같이 담아서, 갤럭시 폴드가 담겨 있는 장바구니 페이지를 우리에게 돌려주는 것이다.

쿠키는 브라우저가 종료되더라도 만료 기간 전까지 계속 저장된다. 이때 만료 기간은 내가 설정할 수 있다. 그래서 동일한 사이트에 재 방문했을 때 요청 시 필요에 따라 쿠키가 재 전송되는 것이다.

크롬을 사용하면 쿠키는 내 크롬(브라우저)에 저장되는 것이지, 서버와는 전혀 상관 없다. 비연결성(Connectionless), 무 상태(Stateless)라는 특성을 가지고 있고, 그러한 것들을 해결하기 위해 서버가 브라우저에게 "야 클라이언트, 네가 일단 저장해놔. 나는 확인만 할게"라고 하는 것이다.

<br>

## Cookie 특징

* 이름(key), 값(value), 만료일(Expire date), 도메인경로(path) 등으로 구성된다.
* 클라이언트에 최대 300개의 쿠키를 저장할 수 있다.
* 하나의 도메인(= 주소) 당 20개의 쿠키를 저장할 수 있다.
* 쿠키 하나당 4KB(4096byte) 제한

쿠키를 굉장히 큰 값을 허용해버리면 통신을 할 때 힘들다. 그래서 쿠키의 크기에는 제한이 있다.

<br>

## Cookie 주요 메서드

<table>
    <tr>
        <th>메서드</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>void setComment(String comment)</td>
        <td>쿠키에 대한 설명 설정</td>
    </tr>
    <tr>
        <td>void setDomain(String domain)</td>
        <td>쿠키의 유효한 도메인 설정</td>
    </tr>
    <tr>
        <td>void setMaxAge(int expiry)</td>
        <td>쿠키 유효기간 설정</td>
    </tr>
    <tr>
        <td>void setPath(String path)</td>
        <td>쿠키 유효 디렉토리 설정</td>
    </tr>
    <tr>
        <td>void setValue(String value)</td>
        <td>쿠키 값 설정</td>
    </tr>
    <tr>
        <td>String getComment()</td>
        <td>쿠키 설명 반환</td>
    </tr>
    <tr>
        <td>String getDomain()</td>
        <td>쿠키 유효 도메인 반환</td>
    </tr>
    <tr>
        <td>int getMaxAge()</td>
        <td>쿠키 유효기간 반환</td>
    </tr>
    <tr>
        <td>String getPath()</td>
        <td>쿠키 유효 디렉토리 반환</td>
    </tr>
    <tr>
        <td>String getValue()</td>
        <td>쿠키 값 반환</td>
    </tr>
</table>

위 메서드들 중에 주로 쓰게 되는 메서드는 ```setMaxAge()``` 같은 것들이 있겠다.

<br>

<hr>

<br>

이제 예시 코드를 통해 쿠키에 대해 알아보겠다.

webapp 폴더 안에 있는 <b>WEB-INF 폴더는 JSP 파일들에 다이렉트로 접근하지 못하게 막는 폴더</b>이다. 이 폴더 안에 들어가려면 <b>포워딩(forwarding)으로 들어가야 한다.</b> 내가 무언가 서비스를 만들 때에는 WEB-INF 안에 작성하고 바로 다이렉트로 접근하지 못하게, 서블릿을 통해 포워딩으로 접근하게끔 만들어야 한다.

(그런데 학습 목적으로는 복잡해지니 그냥 webapp 폴더 안에 두겠다.)

우선 webapp 폴더 안에 index.html의 코드는 다음과 같다.

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>대문</title>
</head>
<body>
	<h2>Cookie / Session</h2>
	<ul>
		<li><a href="01_AddCookie.jsp">01_AddCookie.jsp</a></li>
		<li><a href="02_CookieResult.jsp">02_CookieResult.jsp</a></li>
		<li><a href="03_FirstScope.jsp">03_FirstScope.jsp</a></li>
		<li><a href="04_SecondScope.jsp">04_SecondScope.jsp</a></li>
		<li><a href="05_ThirdScope">05_ThirdScope</a></li>
		<li><a href="06_SessionInfo.jsp">06_SessionInfo.jsp</a></li>
		<li><a href="07_LoginForm.jsp">07_LoginForm.jsp</a></li>
		<li><a href="08_Main.jsp">08_Main.jsp</a></li>
	</ul>
</body>
</html>
```

그리고 webapp 폴더 안에 01_AddCookie.jsp 파일의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>쿠키생산공장</title>
</head>
<body>
	<h2>쿠키 설정</h2>
	<form action="make" method="GET">
		이름 : <input type="text" name="key"> <br>
		내용 : <input type="text" name="value"> <br>
		<button>생성</button>
	</form>
</body>
</html>
```

위 코드를 보면, "make"라는 곳에 요청을 보낼 것이고 방식은 "GET" 방식이다. name="key", name="value" 이니 key라는 이름으로, value라는 이름으로 이러한 것들이 전송된다.

그리고 "make" 앞에 슬래시(/)가 있고 없고의 차이는, 슬래시(/)가 있으면 포트 번호 다음부터, 슬래시가 없으면 가장 마지막에 나온 슬래시부터 출발이다. 그런데 이런 것을 하기 싫으면 ```action="/컨텍스트루트/make"``` 의 방식으로 작성하면 된다.

<br>

이제 MakeCookie 라는 서블릿을 생성한다. MakeCookie 클래스의 코드는 다음과 같다.

```java
package com.ssafy.servlet;

import java.io.IOException;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/make")
public class MakeCookie extends HttpServlet{
	private static final long serialVersionUID = 1L;
	
	@Override
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		String key = request.getParameter("key");
		String value = request.getParameter("value");
		
		// 쿠키를 생성
		Cookie cookie = new Cookie(key, value);
		
		// 유효시간 (만료단위) : 초단위
		cookie.setMaxAge(60); // 1분
		
		//쿠키는 remove 메서드 따로 없다 
//		cookie.setMaxAge(0);	//쿠키를 삭제하는 방법
		
		//사용자에게 어떻게 돌려줄지
		response.addCookie(cookie);
		
		response.sendRedirect(request.getContextPath() + "/02_CookieResult.jsp");
	}
	
	// 예시 상황 쿠키 지우고 싶어.
	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		// 쿠키 이름이 id 인 쿠키를 지우고 싶다고 한다면
		for(Cookie c : req.getCookies()) {
			if(c.getName().equals("id")) {
				c.setMaxAge(0);
			}
		}
		// 위의 과정이 쿠키를 삭제하는 행위
			
	}	
}
```

01_AddCookie.jsp 에서 GET 요청으로 넘겼으니, MakeCookie 서블릿에서는 doGet 메서드를 만들어야 한다. 

그리고 doGet() 메서드에서 String key 라는 이름으로 ```request.getParameter("key");```의 방식으로 값을 받아왔다. 마찬가지 방법으로 String value 라는 이름으로 값을 받아왔다.

그리고 받아온 값으로 쿠키 cookie를 생성한다. 이 쿠키에 유효기간을 설정하고 싶다면 ```setMaxAge();```로 기간을 설정한다. 이때 괄호 안에 들어가는 숫자는 1초 단위이기 때문에, ```cookie.setMaxAge(60);```을 썼으면 이 쿠키의 유효 기간은 1분이 된다. 그리고 쿠키를 지우는 방법은 따로 없다. ```cookie.setMaxAge(0);```를 하면 쿠키가 삭제된다.

그리고 쿠키를 사용자에게 돌려주는 것이니, response를 사용해야 한다. ```response.addCookie(cookie);```의 방식으로 하여, 사용자에게 응답을 할 때 쿠키를 담아서 보낸다.

그리고 ```response.sendRedirect(request.getContextPath() + "/02_CookieResult.jsp");```를 하여 요청을 "/02_CookieResult.jsp"로 보낸다.

<br>

02_CookieResult.jsp 의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>쿠키생산공장</title>
</head>
<body>
	<h2>현재 내브라우저에 있는 쿠키 정보</h2>
	<!-- 내가 가지고 있는 모든 쿠키를 전부 보여주고 싶어! -->
	<%
	Cookie[] cookies = request.getCookies();
	if (cookies != null) {
		for (Cookie c : cookies) {
			String key = c.getName();
			String value = c.getValue();
			out.print(key + ":" + value + "<br/>");
		}
	} else {
		out.print("쿠키 없다");
	}
	%>

	<a href="index.html">홈으로</a>
</body>
</html>
```

<br>

내가 가지고 있는 모든 쿠키를 전부 화면에 보여주고 싶다. 이것은 request와 관련이 깊다. ```request.getCookies()```로 request가 가지고 있는 쿠키를 몽땅 불러올 것이다. 그리고 배열로 쿠키가 출력되도록 하였다.

02_CookieResult.jsp로 잘 가는지 알아보겠다. 우선 Run on Server을 한다. 그리고 화면에서 01_AddCookie.jsp 버튼을 클릭한다.

그러면 이름과 내용을 입력할 수 있는 칸이 나온다. 여기에 이름에는 "id", 내용에는 "ssafy"를 입력하고 '생성' 버튼을 클릭한 다음, 개발자 도구를 켠다. 그러면 다음과 같이 출력된다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/50f0a87e-da66-4584-88f9-d6393d8ba57d" width="1000px" />

그러면 위와 같이 JSESSIONID가 생성되었고, id라는 것이 생성되었다.

다시 홈으로 돌아가서 이름에 "name", 내용에 "John"이라고 입력한 다음 생성 버튼을 클릭한다. 그리고 개발자 도구 창을 켜면 다음과 같은 화면이 출력된다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/412f5ec1-703f-4fd1-835c-ba980efb0fb7" width="1000px" />

쿠키가 추가된 것을 볼 수 있다.