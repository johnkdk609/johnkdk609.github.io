---
layout: post
title: JSP 페이지 이동
categories: Spring-SpringBoot
date: 2024-04-06 19:53:00 +0900
---
JSP에서 페이지 이동을 하는 방법에 대해 알아보겠다.

## 페이지 이동

요청(request)을 받아서 화면을 변경하는 방법은 두 가지가 있다.

* <b>포워드 방식</b>

요청이 들어오면 요청을 받은 JSP 또는 Servlet이 직접 응답을 작성하지 않고, 요청을 서버 내부에서 전달하여 해당 요청을 처리하게 하는 방식

그냥 퍼다 나르는 것이라고 생각하면 쉽다. 어떤 요청이 들어왔을 때 내가 바로 처리하지 않고 다른 JSP 파일로 요청을 퍼다 나르는 것이다.

* <b>리다이렉트 방식</b>

요청이 들어오면 내부 로직 실행 후, 브라우저의 URL을 변경하도록 하여 새로운 요청을 생성함으로써 페이지를 이동한다.

<br>

### 포워드 방식

```java
RequestDispatcher dispatcher = request.getRequestDispatcher("이동할 페이지");
dispatcher.forward(request, response);
```

request, response 객체가 전달되어 사용되기 때문에 객체가 사라지지 않는다. 브라우저에는 최초 요청한 주소가 표시된다.

클라이언트가 요청을 보냈는데, JSP나 Java Servlet가 이를 받아서, 또 다른 JSP 혹은 Java Servlet에게 포워딩(forwarding) 하는 것이다. 사용자가 연결한 통로가 없어지지 않고 그대로 다음 페이지까지 연결이 되는 것이다. 응답도 마찬가지로 계속 이어져 있다.

요청을 함과 동시에 응답할 수 있는 통로도 같이 뚫어 넣는데, 그것 또한 같이 연결이 되어 있는 것이다.

<br>

### 리다이렉트 방식

```java
response.sendRedirect("location");
```

response에게 sendRedirect()를 하는 것이다.

이전에 알아본 포워딩 방식을 생각해 봤을 때 그대로 유지하고 다른 데로 가야 하니까, request라는 객체 안에 getRequestDispatcher()를 동작을 시켰다. 반면 sendRedirect()는 응답에다가 새로운 주소를 집어 넣고 "야 다시 보내" 라고 하는 것이다.

브라우저가 새로운 요청을 만들어 내기 때문에 최초 요청 주소와 다른 요청 주소가 화면에 보여진다.

클라이언트가 요청1 을 보냈을 때, JSP나 Java Servlet이 이를 받을 것이다. 그러면 이 요청1을 받은 것이 sendRedirect() 요청을 하면 이 응답이 사용자에게 돌아간다. 이때에는 300번대 코드가 넘어간다. 그러면 이 클라이언트가 300번대 코드를 받고 어떤 URL 주소를 받았으면, 곧바로 다시 한 번 이 URL로 요청을 날린다.

다른 JSP로 가더라도 주소가 바뀌지 않는데, 이 리다이렉트 방식은 내가 지금 어떤 요청을 보냈을 때 처리가 돼서 "다시 다른 요청으로 보내"라고 하면, 이 요청이 새롭게 바뀌어서 가는 것이다.

가장 심플하게 생각할 수 있는 것은 '주소창이 바뀌나 안 바뀌나'이다.

<br>

예시 코드를 통해 알아보겠다.

일단 webapp 폴더 아래에 11_regist.jsp 파일의 코드는 다음과 같다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>등록</title>
</head>
<body>
<h3>사람 등록</h3>
	<form action="main" method="POST">
		<input type="hidden" name="action" value="regist">
		<label>이름 : </label>
		<input type="text" placeholder="이름을 입력하세요" name="name"><br>
		<label>나이 : </label>
		<input type="number" min="0" placeholder="나이를 입력하세요" name="age"><br>
		<input type="radio" id="man" name="gender" value="man">
		<label for="man">남자</label>
		<input type="radio" id="woman" name="gender" value="woman">
		<label for="woman">여자</label>
		
		<fieldset>
			<legend>취미</legend>
			<input type="checkbox" id="sleep" name="hobby" value="sleep">
			<label for="sleep">수면</label>
			<input type="checkbox" id="eat" name="hobby" value="eat">
			<label for="eat">먹기</label>
			<input type="checkbox" id="study" name="hobby" value="study">
			<label for="study">공부</label>
		</fieldset>
		<input type="submit" value="등록">
	</form>
</body>
</html>
```

위 코드를 보면, 일단 "main"이라는 곳에 보낼 것이고, POST 방식으로 보낼 것이다. 그리고 hidden을 했으므로 이는 Front Controller Pattern을 쓰겠다는 것이다.

위 form 태그에서 가장 중요한 속성이 있다면 name 속성들이다. 그리고 fieldset을 이용하고, submit 버튼을 누르는 방식으로 해두었다.

<br>

그리고 dto 폴더에는 Person 클래스를 생성하여 넣는다. Person.java 파일 코드는 다음과 같다.

```java
package com.ssafy.dto;

import java.util.Arrays;

public class Person {
	private String name;
	private int age;
	private String gender;
	private String[] hobbies;

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

<br>

그 다음, 현재 데이터베이스가 없으니 데이터베이스를 대신할 수 있는 manager 폴더를 만들 것이다. 그리고 그 안에 PersonManager 클래스를 생성한다. PersonManager.class의 코드는 다음과 같다.

```java
package com.ssafy.manager;

import java.util.ArrayList;
import java.util.List;

import com.ssafy.dto.Person;

// 메모리 직접 저장해서 관리할 DB 같은 것!
// 싱글턴
public class PersonManager {
	private List<Person> list = new ArrayList<>();
	private static PersonManager manager = new PersonManager();

	private PersonManager() {
	}

	public static PersonManager getInstance() {
		return manager;
	}

	public void regist(Person p) {
		list.add(p);
	}

	public List<Person> getAll() {
		return list;
	}
}
```

메모리에 저장을 하다 보니, 컴퓨터가 껐다 켜지는 순간 초기화가 되어 버린다.

우선 내가 여기서 private 하게 ```List<Person>```이라는 것을 관리하기 위해 ```new ArrayList<>();```를 하여 list를 만들 것이다. 그리고 이를 싱글턴으로 관리하기 위해, 외부에서 생성할 수 없게 생성자를 private으로 막아두고, 외부에서 쓸 수 있게끔 getInstance()를 만든다. 여기서 ```return manager```을 할 것이니, manager을 하나 만든다. 마찬가지로 static으로 해야 한다. 왜냐하면 getInstance()가 static이기 때문이다.

이제 메서드를 두 개 정의해둘 것이다. Person p가 넘어왔을 때 list에 add() 해주는 것이다. 그리고 getAll() 메서드를 만들어 리스트를 리턴하게 해주는 메서드이다.

<br>

우리는 Servlet이라고 하는 것을 등록해서 만들고 싶다. 패키지는 com.ssafy.servlet로 하고, 이름은 MainServlet으로 하여 생성할 것이다. 만약 Create Servlet 방식으로 생성하면, web.xml을 내가 안 만들었는데도 자동으로 생성이 되었고 등록까지 되었다.

MainServlet의 코드는 다음과 같다.

```java
package com.ssafy.servlet;

import java.io.IOException;

import com.ssafy.dto.Person;
import com.ssafy.manager.PersonManager;

import jakarta.servlet.RequestDispatcher;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

public class MainServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;

	@Override
	protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		String action = request.getParameter("action");
		
		switch (action) {
		case "regist":
			doRegist(request, response);
			break;
		case "list":
			doList(request, response);
			break;
		}
	}

	private void doList(HttpServletRequest request, HttpServletResponse response) throws IOException {
		response.sendRedirect("보내");
	}

	private void doRegist(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		String name = request.getParameter("name");
		int age = Integer.parseInt(request.getParameter("age"));
		String gender = request.getParameter("gender");
		String[] hobbies = request.getParameterValues("hobby");
		
		Person p = new Person(name, age, gender, hobbies);
		
		
		PersonManager pm = PersonManager.getInstance(); //싱글턴이니까
		pm.regist(p);
		
		request.setAttribute("person", p);
		
//		RequestDispatcher disp =  request.getRequestDispatcher("/12_result.jsp");
//		disp.forward(request, response);
		
		response.sendRedirect(request.getContextPath()+"/12_result.jsp");
		
	}
}
```