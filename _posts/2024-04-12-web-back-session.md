---
layout: post
title: Session
categories: Spring-SpringBoot
date: 2024-04-12 12:47:00 +0900
---
세션(Session)에 대해 알아보겠다.

## Session

* 사용자가 웹 서버에 접속해 있는 상태를 하나의 단위로 보고 그것을 세션이라고 한다.
* 각 세션은 sessionid를 이용해 구분한다.
* WAS의 메모리에 객체 형태로 저장
* 메모리가 허용하는 용량까지 제한없이 저장 가능
* 쿠키는 클라이언트에 저장되기 때문에 공유 PC의 경우 보안에 취약할 수 있다. 하지만 세션은 서버에 저장되기 때문에 쿠키에 비해 보안이 좋다.
* 사용자(로그인) 정보 및 장바구니 등에 사용한다.

쿠키를 저장하고 있는 주체는 클라이언트이다. 즉 브라우저에서 저장하고 있는 것이다. 그런데 세션은 서버가 저장한다. WAS에 어떠한 메모리 공간을 할당해서, "이것은 ~~~이 저장할 수 있는 세션 영역"으로 저장을 하는 것이다.

어떤 클라이언트가 세션에 접근하려면 sessionid가 필요하다.

그렇다면 클라이언트는 어떤 식으로 서버의 sessionid를 알 수 있을까? <b>쿠키를 통해 가능</b>하다.

이전에 실습을 할 때 생각해보면 계속 JSESSIONID라는 것이 만들어졌다. 이 JSESSIONID를 이용해서 서버에 있는 세션 영역을 사용한다.

쿠키는 컴퓨터가 통신할 때 데이터를 주고받으니 무거우면 힘들 수 있다. 하지만 세션의 경우 서버 컴퓨터가 버티는 한 용량 상관없이 저장을 할 수 있다. (물론 그러면 안 되기는 하겠지만) 쿠키를 썼을 때보다는 여유가 있다.

로그인에 대한 정보는 세션에 저장한다.

<br>

## Session 동작 순서

* 클라이언트가 페이지를 요청
* 서버는 쿠키에 session id 가 있는지 확인
* session id 가 존재하지 않으면 session id를 생성해 쿠키에 쓴 다음 클라이언트로 반환
* 생성된 session id를 이용하여 서버 내 메모리를 생성
* 클라이언트가 다음 요청 시 쿠키에 session id(JSESSIONID)를 포함해 전달하면 서버 내에 저장된 session id와 비교하여 데이터를 조회

<br>

Run on Server을 하고 시크릿 탭을 켠 다음, 링크(http://localhost:8080/BackEnd_03_Cookie_Session/02_CookieResult.jsp)로 들어가면 다음과 같은 화면이 나타난다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b4637b67-3a26-4de5-a610-2276038c1417" width="450px" />

지금 쿠키가 아무 것도 없다.

개발자 도구로 들어가서 Application 탭으로 가면 다음과 같은 것이 있다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/074c9770-847d-4054-b51c-3b1aa99d5602" width="1000px" />

클라이언트와 WAS가 있을 때, 클라이언트가 WAS에 02_CookieResult.jsp 페이지로 가고 싶다고 요청을 보냈다. 그러면 WAS는 넘겨받은 쿠키를 확인해서 session id 가 있는지 확인한다. 그런데 없다면, JSESSIONID라는 쿠키를 만들어서 응답을 할 때 클라이언트에게 쏘는 것이다.

그래서 렌더링할 당시에는 쿠키가 없지만, 쿠키 창을 열어보면 쿠키가 있는 것이다.

그래서 해당 브라우저에서 새로고침을 하면 다시 요청을 보내는데, 이번에는 session id 가 쿠키와 같이 넘어갔고, 이번에는 쿠키를 생성하지 않고 다시 돌려줬으니 쿠키 목록이 이렇게 보인다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/15da5ad2-67e4-44f3-9ad2-ae146eb90dd3" width="1000px" />

이 값은 해시값이다. 이 값은 고정적이지 않다. 서버를 껐다 켜면 바뀐다.

<br>

다음 그림은 세션 시퀀스 다이어그램이다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1314548b-2e8a-40ce-b654-890234138b26" width="480px" />

클라이언트가 웹사이트에 방문하면서 세션이 생성된다. 그러면 응답에다가 세션 ID를 추가해서 보내는데, 쿠키의 형태로 넘어간다.

이후의 모든 요청에는 세션 ID가 쿠키와 같이 넘어가면서 세션 ID를 쓸 수 있게 된다.

<br>

## HttpSession 주요 메서드

<table>
    <tr>
        <th>메서드</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>void setAttributes(String name, Object value)</td>
        <td>session에 지정한 name에 해당하는 객체를 추가</td>
    </tr>
    <tr>
        <td>void setMaxInactiveInterval(int interval)</td>
        <td>사용자가 다음 요청을 보낼 때까지 세션을 유지하는 최대 시간(초 단위)을 설정</td>
    </tr>
    <tr>
        <td>void invalidate()</td>
        <td>현재 세션을 없애고, 속성도 삭제한다.</td>
    </tr>
    <tr>
        <td>String getId()</td>
        <td>현재 세션의 고유 id를 반환</td>
    </tr>
    <tr>
        <td>long getLastAccessTime()</td>
        <td>현재 세션에 클라이언트가 마지막으로 요청을 보낸 시간을 반환(long)</td>
    </tr>
    <tr>
        <td>Object getAttribute(String name)</td>
        <td>name에 해당하는 속성값 반환, 반환형이 Object임에 유의</td>
    </tr>
    <tr>
        <td>long getCreationTime()</td>
        <td>세션이 만들어진 시간 반환</td>
    </tr>
    <tr>
        <td>void removeAttribute(String name)</td>
        <td>세션에서 지정한 이름의 객체를 제거</td>
    </tr>
    <tr>
        <td>Enumeration getAttributeNames()</td>
        <td>세션에서 모든 객체의 이름을 Enumeration 형으로 반환</td>
    </tr>
</table>

위 메서드들 중, setAttribute(), getAttribute()는 모든 영역에서 쓸 수 있으니 필수적으로 알고 있어야 한다.

인터넷 뱅킹 프로그램에 로그인하는 순간 '남은 시간 30분' 하면서 카운팅이 시작된다. 그래서 내가 무언가 더 작업을 하고 싶으면 갱신을 해야 한다. 이것을 컨트롤하는 것이 setMaxInactiveInterval() 이다.

invalidate()는 현재 가지고 있는 모든 세션의 정보를 삭제해 버리는 것이다.

removeAttribute()를 쓰면 세션에서 지정한 이름의 객체를 제거할 수 있다.

<br>

<hr>

<br>

이제 로그인을 하는 실습을 진행해보겠다.

일단 06_SessionInfo.jsp 파일의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>세션정보</title>
</head>
<body>
	<h3>세션 정보를 얻어오는 메소드를 사용하기</h3>
	<%
	String id_str = session.getId();
	long lasttime = session.getLastAccessedTime();
	long createdtime = session.getCreationTime();
	long time_used = (lasttime - createdtime) / 60000;
	int inactive = session.getMaxInactiveInterval() / 60;
	boolean b_new = session.isNew();
	%>
	[1] 세션 ID는 [
	<%=session.getId()%>] 입니다.
	<br>
	<hr>
	[2] 당신이 웹 사이트에 머문 시간은
	<%=time_used%>
	분입니다.
	<br>
	<hr>
	[3] 세션의 유효 시간은
	<%=inactive%>
	분입니다.
	<br>
	<hr>
	[4] 세션이 새로 만들어졌나요?
	<br>
	<%
	if (b_new)
		out.print(" 예! 새로운 세션을 만들었습니다.");
	else
		out.print(" 아니오! 새로운 세션을 만들지 않았습니다.");
	%>
</body>
</html>
```

Run on Server을 하고, 메인 화면에서 06_SessionInfo.jsp 를 클릭하면 다음과 같은 화면이 나온다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/055ab65a-c893-4921-a912-5936739c1574" width="450px" />

그리고 개발자 도구에 들어가서 생성되어 있는 쿠키를 지우고 새로고침하면 다음과 같이 화면에 나타난다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b682148c-0f8c-43a2-88a7-96306a2a8c30" width="1000px" />

한 번 더 새로고침하면 "아니오! 새로운 세션을 만들지 않았습니다."라는 문구가 또 화면에 나온다.

<br>

이번에는 07_LoginForm.jsp 파일의 코드를 보겠다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>로그인페이지</title>
</head>
<body>
	<h3>로그인페이지</h3>
	<form action="07_LoginCheck.jsp" method="POST">
		<input type="hidden" name="action" value="login">
		<input type="text" placeholder="아이디입력" name="id">
		<input type="password" placeholder="비밀번호입력" id="password" name="password">
		<button>로그인</button>
	</form>
</body>
</html>
```

로그인 페이지에서 우리가 오해하는 것 중 하나가, 우리는 나중에 어떤 요청을 보냈을 때 무조건 서블릿으로 보내서 처리하는 형태로 만들 것인데, JSP에서는 화면만 보여주는 것이 궁극적인 방식이기는 하다. 그런데 JSP도 결국에는 서블릿이다. (서블릿으로 바뀐다.) 그래서 자바 로직을 처리할 수 있다.

지금 만들어놓은 로그인 형태는 우리가 원하는 최종적인 형태는 아니다. (이렇게 쓰는 것은 지양한다.)

로그인을 하는데 action이란 "어디에다가 요청을 보낼지"이다. 보통 여기에 ```/main```과 같은 것을 썼다. 그러면 관련되어 있는 요청을 처리할 수 있는 서블릿이 동작을 하게끔 만들어놓았다. 그런데 위 코드를 보면 ```action="07_LoginCheck.jsp"```라고 되어 있다. 방식은 POST로 하겠다는 것이다. 

냅다 보내는 것이니 사실 ```<input type="hidden">```이 필요 없을 수도 있다. 왜냐하면 로그인을 체크하기 위한 관련 로직을 07_LoginCheck.jsp에서 바로 처리를 할 것이기 때문이다. (그냥 배운 게 있으니 써놨다.)

<br>

Run on Server을 하고, 홈 화면에서 07_LoginForm.jsp 버튼을 클릭한다. 그러면 id와 password을 동시에 넣게 되어 있는데, "ssafy", "1234"를 넣고 로그인 버튼을 누른다. 

그러면 서버 컴퓨터에서는 이렇게 요청을 보냈을 때, 07_LoginCheck.jsp에다가 (id=ssafy, password=1234)라는 데이터가 간다. 이게 현재 request라는 통로에 담긴 채로 서버에 갔다. 그러면 관련 서블릿이 없기 때문에 07_LoginCheck.jsp 가 동작한다. 그리고 그 안에서 로직을 처리하는 것이다.

<br>

07_LoginCheck.jsp 파일의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

<%-- 화면을 보여줄게 아니기 때문에 다 지워버렸다. 로그인 처리 용도만 할 코드! --%>

<%
	String id = request.getParameter("id");
	String pw = request.getParameter("password");
	
	// 실제로는 id / pw 를 이용하여 DB에 저장되어 있는 유저가 맞는지를 체크해야 한다. 
	// 아니면 적어도 Manager라도 있어야 했다.. 하지만 지금은 없다. 
	// if ("ssafy".equals(id) && "1234".equals(pw)) {
	if (id.equals("ssafy") && pw.equals("1234")) {
		// 로그인 성공
		// 세션에 정보를 저장할 것이다.
		// request.getCookies() 반복문 돌면서 JSESSIONID 쿠키를 찾아서 값을 이용해서 메모리에 접근을 해야한다.
		
		/* HttpSession mySession = request.getSession();
		// 이름을 session 이라고 하면 X?
		mySession.setAttribute("id", id); */
		
		session.setAttribute("id", id); // JSP가 session 영역을 기본으로 접근할 수 있으니까... 
		response.sendRedirect("08_Main.jsp"); 
		// request.getRequestDispatcher("08_Main.jsp").forward(request, response);
		// 살짝 위험한 방법이다..
		
	} else {
		// 로그인 실패
		response.sendRedirect("07_LoginForm.jsp");
	}
%>
```

일단은 임시방편으로 id와 pw를 지정하고 비교를 하였다. id를 "ssafy", pw를 "1234"로 입력할 경우에만 통과하게 설정한 것이다. (원래는 DB를 사용하거나 최소한 Manager을 사용해야 한다.) 위와 같이 코드를 짜면 위험할 수 있다.

다음과 같이 직접 URL로 접근하면 NullPointerException이 발생한다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b77a6cf3-519d-4e1f-8c5c-dfb7d809cd5c" width="700px" />

물론 코드를 짤 때 이렇게 다이렉트로 접근할 수 없게 막을 것이다. 하지만 이렇게 들어오려고 했을 때, request 영역에 든 게 없어서 id가 null로 초기화되어 있다. 그런데 어떻게 ```id.equals()```가 가능하냐고 되묻는 것이다.

혹시라도 에러가 발생할 수 있으니 좀 더 안전하게 쓰려면 ```if (id.equals("ssafy") && pw.equals("1234")) {```로 입력하였다. (하지만, 이는 지금 당장의 에러를 때우기 위함이고 크게 의미가 없다.그냥 말 그대로 잔가지만 해결한 것이다.)

다시 돌아와서, 로그인을 성공하면 세션에 정보를 저장해야 한다. 세션에 정보를 저장하려면 세션을 불러와야 한다. 세션을 불러오기 위해서는 내가 가지고 있는 쿠키에 ```request.getCookies();```를 통해 반복문을 돌면서 JSESSIONID라고 하는 쿠키를 찾아서 값을 이용해서 메모리에 접근을 해야 한다. 그런데 이렇게 하면 코드가 너무 길어진다.

세션에다가는 패스워드를 저장하지 않는다.

그리고 ```session.sendRedirect("08_Main.jsp");```를 통해 로그인이 성공하면 Main.jsp로 보낸다.

Main.jsp 파일의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>메인화면</title>
</head>
<body>
	<%
		if (session.getAttribute("id") == null){
			response.sendRedirect("07_LoginForm.jsp");
		} else {
			%>
			<%-- <%=request.getParameter("password") %> 위험해! --%>
			<%=session.getAttribute("id") %>
			<%
		}
	%>
	<!-- 로그아웃 -->
	<a href="07_Logout.jsp">로그아웃</a>
	<form action="07_Logout.jsp" method="POST">
		<input type="submit">
		<button>로그아웃</button>
	</form>
	
	<hr>
	<h2>메인페이지</h2>
	
</body>
</html>
```