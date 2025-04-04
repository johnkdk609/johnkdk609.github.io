---
layout: post
title: Java NullPointerException
categories: Java
date: 2024-10-28 22:34:00 +0900
---
자바(Java)를 다루다 보면 자주 마주치는 것이 NullPointerException 이다. NullPointerException 은 null 때문에 발생하는 Runtime Exception 이다.

이름 그대로 null 을 가리키다(Pointer)인데, 이때 발생하는 예외(Exception)이다.

'null' 은 '없다'는 뜻이므로 결국 주소가 없는 곳을 찾아갈 때 발생하는 예외이다. 객체를 참조할 때에는 ```.``` 을 사용한다. 이렇게 하면 참조값을 사용해서 해당 객체를 찾아갈 수 있는데, 참조값이 ```null``` 이라면 값이 없으므로 찾아갈 수 있는 객체(인스턴스)가 없다.

즉, NullPointerException 은 ```null``` 에 ```.``` 을 찍어서 접근하려 할 때 발생한다.

<br>

자바를 다루다가 NullPointerException 을 예방한 사례를 보자.

다음은 서블릿(Servlet)을 사용하여 간단히 로그인을 시도한 사례이다. 제대로 된 로그인을 구현한 케이스는 아니고, 단지 서블릿을 공부하기 위해 연습한 사례이다.

```java
public class LoginServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String idValue = request.getParameter("id");
        String pwdValue = request.getParameter("pwd");
        String msg = "로그인 실패";

        if ("admin".equals(idValue) && "admin".equals(pwdValue)) {
            msg = "관리자모드 로그인 성공";
        } else if (idValue != null && idValue.equals(pwdValue)) {
            msg = "사용자모드로 로그인 성공";
        }
    }
}
```

위 코드에서 ```"admin".equals(idValue) && "admin".equals(pwdValue)``` 부분을 보면, 아이디와 비밀번호가 모두 "admin" 일 경우에 "관리자모드 로그인 성공" 메세지가 화면에 뜨게 하려는 것을 알 수 있다.

그런데 평소에 내가 코드를 작성 하던대로 했으면 ```idValue.equals("admin") && pwdValue.equals("admin")``` 의 방식으로 했을 것이다. 하지만, 위와 같이 작성함으로써 NullPointerException 의 발생 가능성을 없앴다.

왜냐하면 ```request.getParameter("id");```, ```request.getParameter("pwd");``` 의 방식으로 idValue, pwdValue를 가져올 때, 경우에 따라서 request에 "id" 값이나 "pwd" 값이 없어서 null 일 수 있는데, 그러면 'null 값이 "admin" 과 일치하는 경우' 를 뜻하므로 NullPointerException 이 발생하게 된다. 하지만 "admin"을 앞에 배치함으로써 이러한 NullPointerException 의 가능성을 없앤 것이다.

만약 이렇게 복잡하게 고려를 하고 싶지 않다면, 아얘 처음부터 null 체크를 하는 방법이 있다.

```java
@WebServlet(value="/login")
public class LoginServletAns extends HttpServlet {

    @Override
    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();

        String id = request.getParameter("id");
        String pwd = request.getParameter("pwd");
        if (id == null || pwd == null || id.equals("") || pwd.equals("")) {
            out.println("<h1>아이디, 패스워드를 입력하세요.</h1>");
            out.println("<hr>");
            out.println("<a href='login.html'>로그인폼</a>");
        }

        if (id.equals("admin") && pwd.equals("admin")) {
            // 로그인 성공
            out.println("<h3>관리자 모드로 로그인 하셨습니다.</h3>");
            out.println("<hr>");
        } else if (id.equals(pwd)) {
            out.println("<h3>사용자 모드로 로그인 하셨습니다.</h3>");
        } else {
            // 로그인 실패
            out.println("<h3>로그인 실패 했습니다.</h3>");
            out.println("<a href='login.html'>다시 로그인</a>");
        }
    }
}
```

위 코드를 보면, 일단 id 와 pwd 를 ```request.getParameter()``` 로 가져왔다. 그리고 먼저 조건문으로 null 체크를 했다. null 이거나 빈 값이면 다시 로그인을 하라고 명령하는 것이다.

그 다음에 ```if (id.equals("admin") && pwd.equals("admin")) {``` 와 같은 방식으로 탐색을 했다.

나는 이러한 방식을 더 선호한다.