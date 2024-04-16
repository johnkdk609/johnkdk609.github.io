---
layout: post
title: JSTL (JSP Standard Tag Library)
categories: Spring-SpringBoot
date: 2024-04-16 14:16:00 +0900
---
이제 <b>JSTL</b>에 대해 알아보겠다.

## JSTL (JSP Standard Tag Library)

* JavaEE 기반의 웹 어플리케이션 개발을 위한 컴포넌트 모음
* JSP 스크립트와 html 코드가 섞여서 복잡한 구조를 가짐. 이를 간결하게 작성하기 위해서 자바 코드를 태그 형태로 작성해 놓은 것
* 유용한 커스텀 태그들을 모아서 표준화 한 것

지금까지 HTML 코드 안에 Java 코드를 넣었는데, 생각보다 지저분하다고 생각하는 사람이 많다. 이것을 없애기 위해 EL을 사용했다. 그런데 EL을 통해서 반복문을 돌리는 등의 방법이 없었으니, JSTL을 통해서 반복문, 조건문을 다루고 Java 냄새를 최대한 뺄 것이다.

자바 코드를 태그 형태로 작성해놓은 것이니, 사실 JSTL만 잘 익혀놨다면 자바 코드를 모르더라도 JSP를 만들 수 있다.

<br>

## JSTL 기능

JSTL의 기능들로는 다음과 같은 것이 있다.

* 간단한 프로그램 로직 구현 기능 - 변수 선언, if 문장, for 문 등
* 데이터 출력 포맷 설정
* DB 입력, 수정, 삭제, 조회 기능
* 문자열 처리 함수
* XML 문서 처리

(그런데 기능들 중 간단한 프로그램 로직 구현 기능 정도만 실질적으로 사용하고, 나머지 기능들은 더 유용한 다른 기술들로 대체할 것이다.)

<br>

## JSTL 사용하기

JSTL을 사용하기 위해서는 jar 파일 혹은 Maven 프로젝트로 변경시켜야 한다.

> Jakarta Standard Tag Library API >> 3.0.0
> Jakarta Standard Tag Library Implementation >> 3.0.1

위 두 개를 넣어야 사용할 수 있다. mvnrepository에서 "jstl"이라고 검색하고 가져온다.

외부에서 받은 jar 파일을 추가하려면 프로젝트에 마우스 우클릭을 하고, Build Path > Configure Build Path > Libraries > Classpath 에서 Add JARs 버튼을 클릭하여 집어넣을 수 있다. 

<br>

Dynamic Web Project에서 jar 파일을 추가하는 것은 훨씬 더 간단해졌다. WEB-INF 폴더 안에는 라이브러리들을 모아놓는 lib 폴더가 있다. 이 lib 폴더 안에 다운로드 받아놓은 jar 파일들을 복사해서 넣으면 된다. 

이 방법이 싫다면 Maven 프로젝트로 변경 후 pom.xml 파일에 코드를 추가하면 된다.

<br>

JSTL을 사용하려면 우선 taglib 지시자를 이용해 태그 사용 선언을 해야 한다.

```jsp
<% taglib prefix="c" uri="jakarta.tags.core" %>
```

사용하고자 하는 기능에 따라 어떤 라이브러리를 사용할지 작성한다. (ex: core) URI를 결정해야 하는데, 매번 URI를 쓰기 좀 힘드니, 사용할 태그를 구분하기 위해서 prefix(접두어)를 작성해준다.

```jsp
<c:out value="Hello! JSTL" />
```

위 코드를 보면, c라고 하는 namespace 안에 "out"이라고 이미 정의되어 있는 기능을 사용하겠다는 것이다. prefix에 작성한 접두사를 적어주고, 기능에 따른 태그를 선택하여 작성한다.

<br>

예시 코드를 보자. 07_HelloJSTL.jsp의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="jakarta.tags.core" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JSTL</title>
</head>
<body>

	<h3>C:out</h3>
	<c:out value="Hello JSTL1"></c:out> <br>
	<c:out value="Hello JSTL2"/> <br>
	
	<hr>
	<h3>C:set</h3>	
	<c:set var="name" value="Kim"/> ${name } <br>
	<c:set var="name2">Jang</c:set> ${name2 } <br>
	
	<c:set var="person" value="<%=new com.ssafy.dto.Person() %>"/>
	<c:set target="${person}" property="name" value="Kim2"/>
	${person} <br>

</body>
</html>
```

Run on Server을 하고 07_HelloJSTL.jsp를 클릭하면 다음과 같은 화면이 출력된다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b6687235-2bbd-4340-9179-1e20dd6e2851" width="500px" />

위 코드에서 태그 안에 적을 내용이 없다면, ```<c:out value="Hello JSTL2"/>```와 같이 그냥 하나의 태그로 닫아도 된다.

prefix를 "c"로 하지 않아도 정상적으로 잘 출력된다. URI의 식별자를 가령 "f"로 바꿔도 상관없는 것이다. 하지만, 관례이기 때문에 core은 "c"로 쓰는 것이 맞다.

관례로 쓰이는 것은 다음과 같다.

<table>
    <tr>
        <th style="text-align: center;">라이브러리</th>
        <th style="text-align: center;">접두어</th>
        <th style="text-align: center;">URI</th>
        <th style="text-align: center;">기능</th>
    </tr>
    <tr>
        <td style="text-align: center;">코어</td>
        <td style="text-align: center;">c</td>
        <td>jakarta.tags.core</td>
        <td>변수 선언, 제어문, 페이지 이동 등의 기능 제공</td>
    </tr>
    <tr>
        <td style="text-align: center;">포맷</td>
        <td style="text-align: center;">fmt</td>
        <td>jakarta.tags.fmt</td>
        <td>숫자, 날짜, 시간의 포맷팅 기능, 국제화 기능 제공</td>
    </tr>
    <tr>
        <td style="text-align: center;">함수</td>
        <td style="text-align: center;">fn</td>
        <td>jakarta.tags.functions</td>
        <td>문자열, 컬렉션 처리 등의 다양한 함수 제공</td>
    </tr>
    <tr>
        <td style="text-align: center;">데이터베이스</td>
        <td style="text-align: center;">sql</td>
        <td>jakarta.tags.sql</td>
        <td>데이터베이스에 데이터를 삽입/수정/삭제/조회 기능 제공</td>
    </tr>
    <tr>
        <td style="text-align: center;">XML</td>
        <td style="text-align: center;">x</td>
        <td>jakarta.tags.xml</td>
        <td>XML 문서를 처리할 때 필요로 하는 기능을 제공</td>
    </tr>
</table>

(<a href="https://jakarta.ee/specifications/tags/3.0/jakarta-tags-spec-3.0" target="_blank">Jakarta Standard Tag Library 공식 홈페이지</a>에 들어가면 자세히 볼 수 있다.)

특히 JSTL - Core 에 대해서 좀 더 구체적으로 보면 다음과 같다.

<table>
    <tr>
        <th style="text-align: center;">기능분류</th>
        <th style="text-align: center;">태그</th>
        <th style="text-align: center;">설명</th>
    </tr>
    <tr>
        <td style="text-align: center;" rowspan="2">변수 지원</td>
        <td>set</td>
        <td>JSP에서 사용할 변수를 지정된 범위의 속성으로 설정한다.</td>
    </tr>
    <tr>
        <td>remove</td>
        <td>설정한 변수를 제거한다.</td>
    </tr>
    <tr>
        <td style="text-align: center;" rowspan="4">흐름 제어</td>
        <td>if</td>
        <td>조건에 따라 내부 코드를 수행한다.</td>
    </tr>
    <tr>
        <td>choose</td>
        <td>다중 조건을 처리할 때 사용한다.</td>
    </tr>
    <tr>
        <td>forEach</td>
        <td>컬렉션의 각 항목에 대해 반복 처리할 때 사용한다.</td>
    </tr>
    <tr>
        <td>forTokens</td>
        <td>구분자로 분리된 각각의 토큰을 처리할 때 사용한다.</td>
    </tr>
    <tr>
        <td style="text-align: center;" rowspan="3">URL 처리</td>
        <td>import</td>
        <td>URL을 사용하여 다른 자원의 결과를 삽입한다.</td>
    </tr>
    <tr>
        <td>redirect</td>
        <td>지정한 경로로 리다이렉트 한다.</td>
    </tr>
    <tr>
        <td>url</td>
        <td>URL을 재작성한다.</td>
    </tr>
    <tr>
        <td style="text-align: center;" rowspan="2">기타 태그</td>
        <td>catch</td>
        <td>예외 처리에 사용한다.</td>
    </tr>
    <tr>
        <td>out</td>
        <td>변수나 표현식의 결과를 HTML 출력에 쓸 때 사용한다.</td>
    </tr>
</table>

<br>

다시 위의 07_HelloJSTL.jsp 코드를 보자.

이번에는 ```c:set```을 보자. 공식 문서에서는 해당 구문에 대해 다음과 같이 설명한다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/cd7c8cc1-d27f-4e41-ac89-242cb16ab4c9" width="700px" />

일단 딱 봐도 var은 변수 이름이다. 그리고 value는 값이다. 

그리고 scope라는 것을 사용할 수 있는데, 보면 page, request, session, application을 쓸 수 있다고 한다. 

웬만한 공식문서에서 중괄호({})가 있으면, "이것들 중에 하나 쓰면 돼" 라는 뜻이다. 그리고 밑줄이 그어져 있다는 것은 그것이 default라는 것이다. 그리고 대괄호([])가 등장하면 그것은 옵션이라는 뜻으로 생략 가능하다는 것이다.

<br>

```c:set```의 경우 변수명 name의 경우 value를 "Kim"으로 지정하고, 변수명 name2의 경우 value를 직접 쓰는 것이 아니라 body에 "Jang"을 입력했다.

그래서 Kim과 Jang이 화면에 출력된 것이다.

```jsp
<c:set var="person" value="<%=new com.ssafy.dto.Person() %>"/>
<c:set target="${person}" property="name" value="Kim2"/>
${person} <br>
```

부분을 보면, 변수명은 "person"이고 값(value)은 Person의 생성자를 호출하였다. 이것은 즉, "person" 이라고 하는 변수의 값으로 ```new Person()```을 하여 담은 것이다. 이렇게 풀 패키지명으로 쓰면 import를 따로 하지 않아도 된다.

그리고 이번에는 target 속성을 사용했다. target 속성에는 객체를 쓴다. 그리고 property는 설정으로 "name"이라 쓰고 value는 "Kim2"라고 하였다.

그러고 나서 ```${person}```을 입력해 출력을 하였다. 그래서 위와 같이 값들이 출력되는 것이다. 이때 property는 세터를 호출하는 것이다. 게터가 그냥 가져와서 쓰는 것이라면, 세터는 직접 내가 값을 입력하고 싶을 때 사용하는 것이다. 

<br>

이번에는 다른 코드를 보겠다. 08_FruitSelect.jsp 의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>과일선택</title>
</head>
<body>
	<form action="08_FruitResult.jsp">
		<select name="fruit">
			<option value="1">파인애플</option>
			<option value="2">망고스틴</option>
			<option value="3">멜론</option>
			<option value="4">사과</option>
		</select> 
		<input type="submit">
	</form>
</body>
</html>
```

위 코드에서 ```action="08_FruitResult.jsp"```은 "어디에다가 보낼까요?" 이다. 즉, 08_FruitResult.jsp로 보내는 것이다. 이때 form 태그에 ```method="~~~"```을 입력할 수 있는데, 이것이 생략돼 있으면 기본적으로 GET 방식이 적용된다.

그리고 select 태그를 이용해서 value를 1, 2, 3, 4로 주고 과일을 고르게 하였다. 

08_FruitResult.jsp의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="jakarta.tags.core"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>과일선택</title>
</head>
<body>
	<!-- else 가 없다 -->
	<h3>C:if</h3>
	<c:if test="${param.fruit == 1}">
		<div style="color: yellow">파인애플</div>
	</c:if>
	<c:if test="${param.fruit == 2}">
		<div style="color: pink">망고스틴</div>
	</c:if>
	<c:if test="${param.fruit == 3}">
		<div style="color: green">멜론</div>
	</c:if>
	<c:if test="${param.fruit == 4}">
		<div style="color: red">사과</div>
	</c:if>
	
	<!-- if / else if / else 느낌으로 -->
	<h3>C:Choose</h3>
	<c:choose>
		<c:when test="${param.fruit == 1}">
			<div style="color: yellow">파인애플</div>
		</c:when>
		<c:when test="${param.fruit == 2}">
			<div style="color: pink">망고스틴</div>
		</c:when>
		<c:otherwise>
			<div>그 외의 과일입니다.</div>
		</c:otherwise>
	</c:choose>

</body>
</html>
```