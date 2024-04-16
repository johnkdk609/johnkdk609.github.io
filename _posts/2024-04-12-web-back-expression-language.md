---
layout: post
title: EL (Expression Language)
categories: Spring-SpringBoot
date: 2024-04-12 17:13:00 +0900
---
EL에 대해서 알아보겠다. 그런데 그 전에 지금까지 공부한 요청과 응답의 흐름을 효율적으로 공부하기 위한 방법부터 알아보겠다.

지금 코드를 작성할 때 요청과 응답의 흐름에 걸맞지 않게 MainServlet에 doRegist(), doList(), doLogin() 등의 메서드를 전부 다 작성해놓고 그 다음에 필요한 페이지를 만들어간다. 이런 방식은 내가 나중에 요청과 응답이 익숙하고 이 모든 것이 설계가 끝났을 때 바로 작성하는 것은 괜찮더라도, 공부하는 데에는 도움이 별로 되지 않는다.

공부할 때에는 다음과 같이 해야 한다. 가령 "내가 로그인을 할래"라고 한다면, 일단 로그인을 할 수 있는 LoginForm.jsp 페이지를 만들고, 그리고 나서 이 페이지에서 MainServlet에게 요청을 보낼 것이다. 이때 MainServlet에서 그것을 받아서 doLogin() 메서드를 만드는데, 이 메서드를 이때 쭉 정의해서 쓰는 것이다. 또 어디선가 regist()로 간다고 한다면, Regist.jsp를 만드는 방식으로 해야 하는 것이다.

즉, <b>기능별로 만들어야 흐름이 이해가 잘 되지</b> 파일별로 만들면 흐름이 이해가 잘 안 될 수 있다.

<br>

## EL (Expression Language)

JSP에서 활용할 수 있는 태그는 총 몇 가지일까? 총 5가지이다.

1. 스크립트릿 ```<%  %>``` - 자바 코드를 작성할 수 있고, Servlet으로 변경 후 서블릿 안에 있는 service() 메서드 안에 정의가 될 수 있다. 
2. 선언부 ```<%!  %>``` - 함수, 필드를 정의할 때 사용. 클래스 영역에 작성이 된다. 그리고 기본적으로 서블릿은 싱글턴으로 관리하고 있기 때문에, 필드(멤버)변수로 작성하더라도 공유를 한다. 그래서 static을 안 붙여도 괜찮을 수도 있다. (이런 부분을 고민해봐야 한다.)
3. 표현식 ```<%=  %>``` - 내부적으로 ```out.print();```가 호출된 상황. 이 괄호 안에 내용이 인자로 들어가니까, 세미콜론을 안에 적지 않는다. 그냥 출력을 할 수도 있고, 함수 결과를 쓸 수도 있고, 계산식을 쓸 수도 있다.
4. 주석 ```<%--  -->``` - JSP의 주석은 서블릿으로 변환할 때 가지고 가지 않는다. (변환되지 않는다) 스크립트릿 안에 // 로 주석을 남긴 것은 변환되어 같이 날라간다. 그런데 JSP의 주석은 클라이언트에게 노출되지 않는 것이다.
5. 지시자 ```<%@  %>``` - 크게 세 가지 종류로 나뉜다. page, include, taglib이다. page는 해당 JSP가 가져야 할 설정들을 넣어둔다. include는 어떤 JSP를 다른 JSP에 끼워 넣을 때 사용한다. taglib은 이미 만들어져 있는, 편하게 쓸 수 있는 라이브러리를 가져다 쓸 때 사용한다.

<br>

EL(Expression Language) 표현식에 대해 알아보자.

EL 표현식은 JSP 내에 데이터를 다루는 데 사용되는 스크립팅 언어이다. JSP 스크립트의 표현식(```<%=  %>```)을 대신하여 속성값을 쉽게 출력할 수 있도록 고안된 언어이다.

> 스크립트 표현식 : ```<%= expr %>``` &nbsp;&nbsp;&nbsp;&nbsp; EL: ```${expr }```

<b>${}</b> : <u>변수, 속성, 메서드 호출 등을 포함할 수 있음</u>

<br>

예시 코드를 통해 알아보자. 우선 01_El.jsp의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>EL</title>
</head>
<body>
	<% out.print("Hello"); %> <!-- 스크립트릿 -->
	<%= "Hello" %> <!-- 표현식 -->
	${"Hello"} <!-- EL -->
</body>
</html>
```

"Hello"라는 문자열을 출력하는 다양한 방법이 보인다. 위 코드를 Run on Server로 확인하면 다음과 같다.

<img width="512" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/9c8ec932-6533-4c0a-8c13-57ead87dc326">

<br>

이번에는 02_El.jsp 파일을 보자. 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>EL</title>
</head>
<body>
	문자열 : ${"Kim"}<br>
	정수형 : ${10}<br>
	실수형 : ${10.12} <br>
	논리형 : ${true} <br>
	null : ${null} <br>
</body>
</html>
```

출력 결과는 다음과 같다.

<img width="514" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/bad5099d-3fe5-46c7-8b03-964f652b931d">

다 잘 나왔는데, 특이하게 null에는 아무것도 찍혀있지 않다. EL을 사용했을 때, 어떤 객체가 null인 상황이라면 이것은 그냥 아무것도 안 띄워버린다. null이라는 값이 찍히는 것이 아니고, EL을 사용하면 아무것도 안 뜨는 것이다.

<br>

## JavaBeans

JavaBeans에 대해 얘기해보겠다.

JavaBeans는 Java로 작성된 재사용 가능한 소프트웨어 컴포넌트이다.

* 관례
    * 클래스는 직렬화 되어야 한다.
    * 클래스는 기본 생성자를 가지고 있어야 한다.
    * 클래스의 속성들은 getter, setter를 사용해 접근할 수 있어야 한다.
    * 클래스는 필요한 이벤트 처리 메서드들을 포함하고 있어야 한다.

* JavaBeans에는 EL 표현식을 이용하여 접근이 가능하다.
    * .(dot) 혹은 ["속성명"]을 사용할 수 있다.
    * 인스턴스만을 작성하면 toString() 메서드가 동작한다.

예시 코드를 보면 다음과 같다. 우선 dto 패키지의 Person 클래스는 다음과 같다.

```java
package com.ssafy.dto;

import java.io.Serializable;
import java.util.Arrays;

public class Person implements Serializable {
	private static final long serialVersionUID = 1L;
	private String name;
	private int age;
	private String gender;
	private String[] hobbies;
//	private boolean flag; 사용하지는 않을 것 

	public Person() {
	}

	public Person(String name, int age, String gender, String[] hobbies) {
		super();
		this.name = name;
		this.age = age;
		this.gender = gender;
		this.hobbies = hobbies;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

	public String getGender() {
		return gender;
	}

	public void setGender(String gender) {
		this.gender = gender;
	}

	public String[] getHobbies() {
		return hobbies;
	}

	public void setHobbies(String[] hobbies) {
		this.hobbies = hobbies;
	}

	@Override
	public String toString() {
		return "Person [name=" + name + ", age=" + age + ", gender=" + gender + ", hobbies=" + Arrays.toString(hobbies)
				+ "]";
	}
}
```

위와 같은 규칙을 가진 코드를 자바 빈이라고 한다. 그런데 조금만 더 추가하자면, 직렬화를 위해서 Serializable 인터페이스를 implements 할 수 있고, 이를 위해 간단히 한 줄 코드를 추가했다. 그리고 boolean 타입의 flag 변수는 getter의 경우 getFlag이 아니라 'isFlag' 로 생성되는 것이 관례이다. (이번에는 flag 변수는 쓰지 않을 것이다.)

<br>

이제 위에서 생성한 자바 빈을 JSP에서 사용해보겠다. 03_JavaBean.jsp의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ page import="com.ssafy.dto.Person"%>
<%
	Person p = new Person();
	p.setName("John");
	p.setAge(28);
	p.setHobbies(new String[]{"공부", "주짓수"});
	pageContext.setAttribute("p", p);
%>
	
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JavaBean</title>
</head>
<body>
	<h2>JavaBeans3</h2>
	<div>전체 정보 : ${p}</div>
	<div>이름 : ${p.name}</div>
	<div>나이 : ${p["age"]}</div>
	<div>취미 : ${p["hobbies"][1]}</div>
</body>
</html>
```

출력 결과는 다음과 같다.

<img width="570" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0cce9d76-958f-4466-ab38-a5077e6c3060">

위 코드를 보면, 자바 빈 Person 클래스를 사용하기 위해 ```<%@ page import="com.ssafy.dto.Person"%>```를 상단에 입력했다. 그리고 스크립트릿을 통해서 Person p를 생성하고, 이름, 나이, 취미 등을 입력했다. 그리고 ```pageContext.setAttribute("p", p);```를 입력하여 현재 "p"라는 이름으로 Person 객체 p를 심는다. 이렇게 해야 JSP에서 p를 사용할 수 있다. (나중에는 직접 안 심고 request로 접근할 것이다.)

그리고 아랫부분에 EL을 사용하여 내용에 접근했는데, 다양한 방법이 있음을 알 수 있다.

<br>

## 연산자 (Operator)

<table>
    <tr>
        <th>종류</th>
        <th>사용 가능 연산자</th>
    </tr>
    <tr>
        <td>산술</td>
        <td>+, -, *, /(div), %(mod)</td>
    </tr>
    <tr>
        <td>관계</td>
        <td>==(eq), !=(nq), <(lt), >(gt), <=(le), >=(ge)</td>
    </tr>
    <tr>
        <td>조건</td>
        <td>expr ? val1 : val2</td>
    </tr>
    <tr>
        <td>논리</td>
        <td>&&(and), ||(or), !(not)</td>
    </tr>
    <tr>
        <td>null</td>
        <td>empty</td>
    </tr>
</table>

(위 표에서 괄호 안은 동일한 의미의 연산자이다.)

* EL 식은 연산자를 포함할 수 있으며, 산술 연산, 조건 연산, 논리 연산, 관계 연산을 수행할 수 있다.
* 일반적인 연산 이외에 null 연산을 수행할 수 있다.
* empty 연산자는 검사할 객체가 null인지 아닌지 검사하기 위해서 사용된다. (null, "", 요소가 없는 list, map, collection은 True)

> ```${ empty name }```

<br>

연산자를 화면에 출력해보자.

04_Op.jsp 파일의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>EL</title>
</head>
<body>
	\${5+2} : ${5+2} <br>
	\${5/2} : ${5/2} <br>
	<%-- \${5 div 2} : ${5 div 2} <br> --%>
	\${5 gt 2} : ${5 gt 2} <br>
	\${5 < 2} : ${5 < 2} <br>
	<!-- 그 외 나머지 들은 직접 작성을 해보면서 익히자 -->
</body>
</html>
```

Run on Server을 하고 04_Op.jsp를 클릭하면 다음과 같은 화면이 나온다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c5b870db-fc77-4a4a-90eb-aaca135116d2" width="500px" />

위 코드를 보면, '$'가 EL로 인식되지 않게 하고 화면에 출력하기 위해서 escape 문자인 '/'를 '$' 앞에 붙였다. 

'div'를 사용한 경우 컴파일이 안 되는 것은 아닌데, 계속 빨간 줄이 뜬다. 그래서 주석처리 하였다.

<br>

다음으로는 "eq" (equals)를 예시 코드로 알아보자. 05_eq.jsp 의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>EL</title>
</head>
<body>
	<!-- request 영역에서 id 파라미터를 꺼내와보자 -->
	<%=request.getParameter("id")%> <br>
	${param.id} <br>
	
	== : <%=request.getParameter("id") == "ssafy" %><br>
	equals() : <%=request.getParameter("id").equals("ssafy") %><br>
	
	== (EL) : ${param.id == "ssafy"}<br>
	eq (EL) : ${param.id eq "ssafy"}<br>

</body>
</html>
```

위 코드를 보면, ```<%=request.getParameter("id")%>```로 해서 request가 가지고 있는 id를 꺼내왔다. 또 다른 방식이 있는데, EL 표현으로 하여 ```${param.id}```을 하는 것이다. param이라는 객체가 있다. param의 key인 id를 꺼내는 것이다. 가령 id = "ssafy" 이면 화면에 둘 다 "ssafy"로 출력된다.

이제 비교를 해보겠다. "==" 연산자로 비교를 할 것이다. 여기서 표현식의 결과를 바로 쓰고 싶으니 ```<%=request.getParameter("id") == "ssafy" %>```를 입력한다.

"equals()"로 파악해보기 위해 ```<%=request.getParameter("id").equals("ssafy") %>```도 입력해본다.

이제 EL 표현으로 사용한 "=="과 "eq"를 알아보겠다. ```${param.id == "ssafy"}``` 및 ```${param.id eq "ssafy"}```을 입력한다.

그런데 현재 그냥 ```<%=request.getParameter("id")%>```를 하면 null이 반환된다. 왜냐하면 request의 "id"가 비어있기 때문이다. 이를 해결하기 위해 URL에 query String을 입력한다.

위 코드의 출력 결과는 다음과 같다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/eb037854-d9a4-46aa-aadf-813d671d1c92" width="500px" />

<br>

## EL (Expression Language) 내장 객체

다음으로 EL에 내장되어 있는 객체를 꺼내서 쓸 때 어떻게 쓰는지 알아보겠다.

<table>
	<tr>
		<th style="text-align: center;">분류</th>
		<th style="text-align: center;">객체</th>
		<th style="text-align: center;">타입</th>
		<th style="text-align: center;">설명</th>
	</tr>
	<tr>
		<td style="text-align: center;">JSP</td>
		<td>pageContext</td>
		<td style="text-align: center;">Java Bean</td>
		<td>현재 페이지의 page context instance</td>
	</tr>
	<tr>
		<td style="text-align: center;" rowspan="4">scope</td>
		<td>pageScope</td>
		<td style="text-align: center;">Map</td>
		<td>page 기본 객체에 저장된 속성을 저장하는 객체</td>
	</tr>
	<tr>
		<td>requestScope</td>
		<td style="text-align: center;">Map</td>
		<td>request 기본 객체에 저장된 속성을 저장하는 객체</td>
	</tr>
	<tr>
		<td>sessionScope</td>
		<td style="text-align: center;">Map</td>
		<td>session 기본 객체에 저장된 속성을 저장하는 객체</td>
	</tr>
	<tr>
		<td>applicationScope</td>
		<td style="text-align: center;">Map</td>
		<td>application 기본 객체에 저장된 속성을 저장하는 객체</td>
	</tr>
	<tr>
		<td style="text-align: center;" rowspan="2">요청 파라미터</td>
		<td>param</td>
		<td style="text-align: center;">Map</td>
		<td>JSP 내장 객체 request의 getParameter(name) 메서드와 동일한 역할을 한다.<br>${param.name} 또는 ${param["name"]}의 형태로 사용한다.</td>
	</tr>
	<tr>
		<td>paramValues</td>
		<td style="text-align: center;">Map</td>
		<td>JSP 내장 객체 request의 getParameterValues(name) 메서드와 동일한 역할을 한다.</td>
	</tr>
	<tr>
		<td style="text-align: center;">쿠키</td>
		<td>cookie</td>
		<td style="text-align: center;">Map</td>
		<td>request 안에 있는 쿠키를 가져올 수 있다.</td>
	</tr>
	<tr>
		<td style="text-align: center;" rowspan="2">요청헤더</td>
		<td>header</td>
		<td style="text-align: center;">Map</td>
		<td>request의 getHeader(name) 메서드와 동일한 역할을 한다.</td>
	</tr>
	<tr>
		<td>headerValues</td>
		<td style="text-align: center;">Map</td>
		<td>request의 getHeaders(name) 메서드와 동일한 역할을 한다.</td>
	</tr>
</table>

EL 표현식도 page, request, session, application 같은 것들을 쓸 수 있는데, JSP가 이미 같은 이름을 가지고 있기 때문에 "pageScope", "requestScope", "sessionScope", "applicationScope"라는 이름으로 접근할 수 있다. 

그리고 param, (여럿이서 들어오면) paramValues 을 위와 같이 쓸 수 있다.

<br>

이제 예시 코드를 보겠다. 06_pageScope.jsp 파일의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>EL</title>
</head>
<body>
	<%
	// pageContext.setAttribute("name", "page kim");
	request.setAttribute("name", "request kim");
	session.setAttribute("name", "session kim");
	application.setAttribute("name", "application kim");
	%>
	
	JSP Page 영역 접근 : <%=pageContext.getAttribute("name") %> <br>
	EL (Page) : ${pageScope.name} <br>
	EL (Request) : ${requestScope.name} <br>
	EL (Session) : ${sessionScope.name} <br>
	EL (Application) : ${applicationScope.name} <br>
	
	그냥 냅다 name 속성 가져와 : ${name } <br>


	쿠키(대괄호 접근) : ${cookie["JSESSIONID"].value} <br>
	쿠키(. dot 접근) : ${cookie.JSESSIONID.value } <br> 

</body>
</html>
```

우선 JSP를 이용해서 pageContext, request, session, application에 "page kim", "request kim", "session kim", "application kim"을 저장했다.

우선 JSP로 한 번 꺼내봤다. 그리고 같은 내용을 EL로 한 번 꺼내봤다. 그리고 나머지 request, session, application도 쭉 꺼내봤다. 

그리고 한 번 냅다 name 속성을 가져와봤다. 냅다 name을 쓰면 page 영역을 가져온다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/bd74e7db-0e39-4375-a5ea-0717fe6f2441" width="500px" />

그런데, 위 코드에서 ```pageContext.setAttribute("name", "page kim");```을 주석처리한 다음 화면을 새로고침 하면 다음과 같이 나온다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7d125f44-7f65-4887-ae09-b310b6059307" width="500px" />

name 속성이 "page kim"이 아니라 "request kim"으로 바뀐 것이다. 

JSP 영역에는 page 영역이 있었고, 그것보다 큰 request 영역이 있었고, 그보다 큰 session 영역, 그리고 가장 큰 application 영역이 있었다.

이때 <b>아무것도 내가 지정하지 않았으면 나와 가까운 것부터 찾아서 있는지 확인한다.</b> name이라는 속성이 page 영역에 있는지 보는 것이다. 만약 없으면 request 영역에서 찾아보고, 또 없으면 session 영역에서, 그리고 그마저도 없으면 application 영역에서 찾는 것이다.

(그런데 코드를 작성할 때 명확하게 작성하면 좋지만, 위와 같이 그냥 name 속성을 가져오는 경우가 허다하다.)

<br>

그리고 쿠키를 가져오기 위해서 위 코드에서는 ```${cookie["JSESSIONID"].value}```의 방식으로 접근하였다. 이처럼 대괄호로 접근할 수 있고, ```${cookie.JSESSIONID.value }``` 방식처럼 .(dot)으로 접근할 수도 있다. 이때 ```.value```를 붙여야 값이 나온다. (이것을 뒤에 붙이지 않으면 객체 주소값이 나온다.)