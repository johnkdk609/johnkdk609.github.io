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

<b>즉, 컨트롤러가 정적 파일보다 우선순위가 높다.</b>

위의 home.html을 보면, 링크가 두 개 있다.

```html
<a href="/members/new">회원 가입</a>
<a href="/members">회원 목록</a>
```

/members/new의 회원 가입 화면으로 이동하는 것과, /members의 회원 목록 화면으로 이동하는 것이 있다.

## 회원 웹 기능 - 등록

이번에는 회원 웹 기능 중 '등록'을 만들 것이다.

이전에 만들어놨던 MemberController에 들어가서, ```@GetMapping("/members/new")```를 기입한다. 이렇게 하면 직전에 만든 home.html에서 "/members/new"라는 URL로 이동한다. 그리고 "members/createMemberForm"을 리턴한다.

templates 디렉토리에 members 디렉토리를 생성한다. 그리고 createMemberForm.html 파일을 members 디렉토리 안에 만든다.

<img width="264" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b8bbcad1-dd40-4bab-8be1-a02670b9d125">

<b>createMemberForm.html</b>의 코드는 다음과 같다. (복사 붙여넣기 하였다.)

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<div class="container">
    <form action="/members/new" method="post">
        <div class="form-group">
            <label for="name">이름</label>
            <input type="text" id="name" name="name" placeholder="이름을 입력하세요">
        </div>
        <button type="submit">등록</button>
    </form>
</div> <!-- /container -->
</body>
</html>
```

<br>

이제 스프링을 구동해보겠다.

웹 브라우저에서 localhost:8080로 들어간 다음, '회원 가입'을 클릭하면 localhost:8080/members/new로 들어간다.

<img width="398" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/fbf9745c-7a48-4b47-bb22-face511dd62b">

페이지 소스 보기를 클릭하면 아래와 같이 방금 했던 html이 렌더링 된다.

<img width="727" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/84bba051-4ce7-4090-8901-85096a91836b">

form태그가 있고, input태그에 이름만 입력하게 되어 있다. 예를 들어 이름을 "spring"으로 등록하면, 이 name이라는 key와 "spring"이라는 value가 서버로 넘어가게 된다. (일단은 등록한 후의 html 파일을 만들지 않았으니 등록을 했을 때 에러가 뜬다.)


<br>

이제 회원을 등록하는 컨트롤러를 만들 것이다. hello.hellospring/controller 패키지 아래에 MemberForm클래스를 생성한다.

<b>MemberForm클래스</b>의 코드는 다음과 같다.

```java
package hello.hellospring.controller;

public class MemberForm {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

먼저 ```private String name;```을 기입하고, Getter Setter를 생성한다. (```cmd + N```을 누르면 된다.) 이렇게 하면 ```private String name;```의 name과 createMemberForm.html의 name과 매칭이 되면서 값이 들어온다.

<img width="771" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5939acd1-d69a-4dc4-99fe-202033b2c009">

<br>

이제 MemberController에 ```@PostMapping("/members/new")```를 기입한다. 해당 부분 코드는 다음과 같다.

```java
@PostMapping("/members/new")
public String create(MemberForm form) {
    Member member = new Member();
    member.setName(form.getName());

    memberService.join(member);

    return "redirect:/";
}
```

create메서드에 form을 넣어준다. member를 생성하고, ```member.setName(form.getName());```를 입력해 form에서 get한 이름으로 member를 세팅한다. 그리고 memberService에 세팅된 member를 join(회원가입)시킨다. 그리고 리턴을 할 때 회원가입이 끝났으니 홈 화면으로 redirect한다.

다시 스프링을 동작해보면, '회원 가입' 버튼을 누르고 "spring"을 등록하면 다시 홈 화면으로 돌아간다. 정상적으로 작동하고 있는 것이다.

<br>

이제 원리를 알아보겠다.

먼저 회원 가입을 클릭해서 들어가면 /members/new로 들어가게 된다. /members/new를 URL에 직접 치면 http의 GET 방식이라고 한다. 그렇게 template 안에 있는 createMemberForm.html으로 이동하게 된다. viewResolver라는 것을 통해서 선택이 되고 Thymeleaf 템플릿 엔진이 createMemberForm.html을 렌더링 한다. (지금은 Thymeleaf가 크게 관여할 것이 없다.)

createMemberForm.html이 뿌려질 때, form이라는 태그가 있다. 이 태그는 값을 입력할 수 있는 html태그이다. 이 form 안에서 보면 action="/members/new"라고 되어 있고 메서드는 post로 되어 있다. 그 안에 input이 있는데, input 타입이 text로 text를 입력할 수 있는 input이 생성된다는 뜻이다. 그리고 name이 중요한데 이 name이 서버로 넘어올 때 key가 된다. placeholder는 아무것도 없을 때 적히는 것이다.

이 name에 "spring"이라고 적고 등록 버튼을 누르면, action URL인 "/members/new"에 POST 방식으로 넘어온다. /members/new에 POST 방식으로 넘어오면 MemberController의 ```@PostMapping("/members/new")```으로 간다.

기본적으로 URL을 창에 직접 치는 것은 GetMapping이고, 데이터를 조회할 때 주로 쓴다. 반면 PostMapping은 데이터를 form 같은 데에 넣어서 전달할 때 사용한다. (보통 데이터를 조회할 때 GET 메서드를, 등록할 때 POST 메서드를 사용한다.)

create라는 메서드가 호출이 되면서 값이 들어온다. 그런데 MemberForm을 보면 ```private String name;```의 name에 "spring"이라는 값이 들어가게 된다.

createMemberForm.html의 ```name = "name"```을 보고 스프링이 MemberForm의 setName을 통해 값이 들어간다.

<img width="771" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5939acd1-d69a-4dc4-99fe-202033b2c009">

private이기 때문에 막 접근할 수는 없고 스프링이 setName이라는 Setter를 이용해서 값을 넣어주게 되고, 우리는 getName으로 꺼내면 된다.

name을 한 번 찍어보겠다.

<img width="935" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5e14d8b8-602b-46ad-b7eb-c612a4e298ce">

위 그림과 같이 한 줄 추가한 다음 스프링을 동작한다. 그리고 이름에 "spring!"을 입력하고 등록하면 콘솔에 아래와 같이 뜨는 것을 볼 수 있다.

<img width="1630" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f0c6c5b5-8942-4846-af8a-c0d579d9378


## 회원 웹 기능 - 조회

이번에는 회원 웹 기능 중 조회를 만들어볼 것이다. 홈 화면에서 '회원 목록'을 눌렀을 때 동작하면 된다.

MemberController클래스에 ```@GetMapping("/members")```를 기입한다. 해당 부분 코드는 다음과 같다.

```java
@GetMapping("/members")
public String list(Model model) {
    List<Member> members = memberService.findMembers();
    model.addAttribute("members", members);
    return "members/memberList";
}
```

```memberService.findMembers();```를 하면 member를 다 끌어올 수 있다. 그리고 ```model.addAttribute("members", members);```를 기입해서 멤버의 리스트를 model에 담아서 화면(view)에 넘길 것이다. "members/memberList"를 리턴하니, memberList.html을 만들어야 한다.

<b>memberList.html</b>의 코드는 다음과 같다. (복사 붙여넣기 하였다.)

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<div class="container">
    <div>
        <table>
            <thead>
            <tr>
                <th>#</th>
                <th>이름</th>
            </tr>
            </thead>
            <tbody>
            <tr th:each="member : ${members}">
                <td th:text="${member.id}"></td>
                <td th:text="${member.name}"></td>
            </tr>
            </tbody>
        </table>
    </div>
</div> <!-- /container -->
</body>
</html>
```

한 가지 짚고 넘어가야 하는 것이 있다. 여기서 thymeleaf 템플릿 엔진이 본격적으로 동작을 한다.

일단 스프링을 실행하고, 회원 가입을 누른 다음 "spring1", "spring2"를 각각 등록한 다음 회원 목록 버튼을 누르면 다음과 같이 나타난다.

<img width="333" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/a0d37ede-588f-4b49-8eb8-fe2b96859b1a">

페이지 소스 보기를 해보면 다음과 같이 나타난다.

<img width="442" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/bb210af8-f1c2-4ded-af6c-a56479d5ce64">

위 그림을 보면 tr태그 안에 1, 2 이렇게 두 개가 렌더링 되어 있다. 위의 memberList.html 코드에서 ```${members}```의 members를 읽어들인다. 이 '${}'는 모델 안의 값을 꺼내는 것이다. 직전에 만든 MemberController의 ```@GetMapping("/members")```를 보면 model에 addAttribute로 key가 "members"이고 value인 members 안에는 리스트로 모든 회원을 다 조회해서 담아놨다. ```th:each```는 thymeleaf 문법으로, 루프를 돌면서 이 로직을 실행하는 것이다. 첫 번째 객체를 꺼내서 member에 담고, id와 name을 출력하는 것이다.

<br>

이렇게 해서 회원 조회까지 다 알아봤다.

메모리에 있기 때문에, 서버를 내렸다가 다시 켜면 데이터가 다 지워진다. 우리는 이 데이터를 파일이나 데이터에 저장해야 한다.