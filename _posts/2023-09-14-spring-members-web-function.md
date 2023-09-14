---
layout: post
title: 회원 웹 기능
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-09-14 17:15:00 +0900
---
이번 시간에는 회원 관리 예제를 웹 MVC로 개발해볼 것이다.

이전 시간에 생성해놓은 MemberController를 통해 회원을 등록하고 조회하는 것을 만들 것이다.

목차는 다음과 같다.

* <b>회원 웹 기능 - 홈 화면 추가</b>
* <b>회원 웹 기능 - 등록</b>
* <b>회원 웹 기능 - 조회</b>

## 회원 웹 기능 - 홈 화면 추가

먼저 홈 화면을 추가해볼 것이다. 아주 단순한 회원을 등록하고 조회할 수 있는 버튼, 링크가 있는 사이트를 만들 것이다.

hello.hellospring/controller 디렉토리 안에 HomeController 클래스를 만든다.

<b>HomeContoller클래스</b>의 코드는 다음과 같다.

```java
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HomeController {

    @GetMapping("/")
    public String home() {
        return "home";
    }
}
```

우선 위에 ```@Controller``` 어노테이션을 붙인다. 그리고 ```@GetMapping("/")``` 을 붙이고 "home"을 리턴할 것이다. GetMapping의 "/"는 도메인 첫 번째에 호출된다는 뜻이다. templates에 있는 home.html이 호출된다.

resources/templates패키지 아래에 home.html 파일을 생성한다.

<b>home.html</b>의 코드는 다음과 같다. (그냥 복사 붙여넣기 하였다.)

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<div class="container">
    <div>
        <h1>Hello Spring</h1>
        <p>회원 기능</p>
        <p>
            <a href="/members/new">회원 가입</a>
            <a href="/members">회원 목록</a>
        </p>
    </div>
</div> <!-- /container -->
</body>
</html>
```

이제 스프링을 실행한다. 웹 브라우저에서 'localhost:8080'을 입력하고 들어가면 다음과 같이 뜬다.

<img width="317" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/3c5bb2c0-804f-4743-98c2-9d712247f5ba">

그런데 여기서 이상한 점을 느낄 수 있다. 이전에 static에 index.html을 만들었었는데, 아무것도 없으면 웰컴 페이지인 index.html로 가는 것으로 알고 있었다. 그런데 우선순위가 있다.

이전에 공부했던 정적 컨텐츠 이미지를 다시 한 번 보겠다.

<img width="872" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ad3d54b7-8624-4e9e-942f-ae6b43eef487">

위 그림을 봤을 때, 먼저 요청이 오면 스프링 컨테이너 안에 관련 컨트롤러가 있는지 먼저 찾고, 없으면 static 파일을 찾도록 되어 있다. 웰컴도 마찬가지이다. 먼저 localhost:8080 요청이 오면 먼저 HomeController에서 찾아본다. '어, 홈 화면에 매핑된 URL이 있네' 하면서 컨트롤러를 호출하고 끝난다. 그래서 기존에 만들었던 index.html은 무시된다. (물론, HelloController를 제거하면 다시 index.html이 등장한다.)

위의 home.html을 보면, 링크가 두 개 있다.

```html
<a href="/members/new">회원 가입</a>
<a href="/members">회원 목록</a>
```

/members/new의 회원 가입 화면으로 이동하는 것과, /members의 회원 목록 화면으로 이동하는 것이 있다.