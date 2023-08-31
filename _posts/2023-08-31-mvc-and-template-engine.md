---
layout: post
title: MVC와 템플릿 엔진
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-08-31 16:29:00 +0900
---
* <b>MVC: Model, View, Controller</b>

과거에는 컨트롤러(Controller)와 뷰(View)가 따로 분리되어 있지 않았었다. 뷰에서 모든 것을 다 했다.(JSP) 이것을 model 1 방식이라고 한다. 지금은 MVC 스타일로 많이 한다.

요즘에는 컨트롤러와 뷰를 쪼개는 것이 기본이다.

'관심사의 분리', '역할과 책임'... 뷰(View)는 화면을 그리는 데에 모든 역량을 집중해야 한다. 컨트롤러나 모델은 비즈니스 로직과 관련이 있거나, 내부적인 것을 처리하는 데에 집중해야 한다. 그래서 모델, 뷰, 컨트롤러로 쪼갠 것이다.

HelloController에 컨트롤러를 하나 더 만들어보겠다.

<img width="1103" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e95b7a97-7018-403e-9b6e-62b3aae46ba4">

@GetMapping("hello-mvc")를 만들고, 이번에는 웹에서 파라미터(parameter)를 받을 것이다. @RequestParam("name")를 입력하고, model에 담아서 넘겨준다.

그리고 model.addAttribute("name", name);를 입력해서 파라미터로 넘어온 name을 넘겨준다. "name"이 key이다.

그리고 "hello-template"를 리턴한다.

이제 resources:templates에 hello-template.html을 생성한다.

<img width="890" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2dfe4f1e-37d2-464c-a801-11960cea64bb">

위 그림에서 p태그 안의 hello! empty 부분은 없어도 되는 부분이다. Thymeleaf 템플릿의 장점이 뭐냐면, html을 그대로 쓰고 서버 없이 파일을 열어봐도 껍데기를 볼 수 있다.

만약 템플릿 엔진이 동작하면, ```'hello ' + ${name}```의 값으로 치환이 된다. 즉, p태그 안에 내용을 넣어두는 것은 서버 없이 html을 만들어서 볼 때(Mock up) 등에 사용하는 것이다.

이제 서버를 실행하고, 웹브라우저에 localhost:8080/hello-mvc를 입력하면 처음에 에러가 난다. 왜 에러가 나는지 알려면 로그를 봐야 한다.

<img width="1309" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e744cc24-b7d4-40fd-9c3b-60293049201c">

'Required String parameter 'name' is not present' 즉, name이 있어야 한다는 것이다.

helloController의 helloMvc의 @RequestParam("name") 부분을 클릭하고 cmd + p 를 누르면 다음과 같이 옵션이 뜬다.

<img width="1106" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/dceaa0a7-b757-46d4-a7f1-5215ec47e64b">

required라는 옵션이 있는데 default가 true이기 때문에 무조건 값을 넣어야 한다. (default가 false이면 안 넘겨도 된다.)

다시 웹브라우저에 localhost:8080/hello-mvc?name=Spring!!!이라고 입력하면 다음과 같이 화면에 출력된다. (물음표('?')를 이용하는 것은 http의 Get 방식이다.)

<img width="478" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7a5bc70c-a631-4f2d-9318-c6718be1beff">

?name=Spring!!! 이라고 넘어가면 컨트롤러에서 name은 "Spring!!!"으로 바뀌고 model에 담긴다. 

그리고 hello-template로 넘어가면 ${name}은 "Spring!!!"으로 바뀌어서 나간다.


## MVC와 템플릿 엔진 동작 원리

MVC, 템플릿 엔진의 동작 방식을 간략히 설명하는 그림은 다음과 같다.

<img width="873" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e1debb17-686e-4708-bd65-e3986e896b48">

웹브라우저에서 localhost:8080/hello-mvc를 넘기면, 스프링 부트는 내장 톰캣 서버를 거친다. 내장 톰캣 서버는 "어 hello-mvc라는 것이 왔어" 하면서 스프링에게 던진다.

그러면 스프링은 "아 helloController의 저 메서드에 매핑이 되어 있네" 하면서 그 메서드를 호출한다. 그리고 리턴할 때에 이름을 "hello-template"로, model에는 key는 name, value는 "Spring!!!"으로 넣었다.

이것을 스프링에게 넘겨주면, 화면과 관련한 해결자인 viewResolver가 동작한다. (view를 찾아주고 템플릿 엔진을 연결해주는 역할을 하는 것)

viewResolver가 templates/hello-template.html이라는 리턴의 String name과 동일한 것을 찾아서 Thymeleaf 템플릿 엔진에게 처리해 달라고 넘긴다.

그럼 템플릿 엔진이 렌더링을 해서 변환을 한 html을 웹브라우저에 반환한다.

정적일 때에는 변환을 하지 않고 그대로 반환을 하였다. 반면 이런 템플릿 엔진에서는 변환을 해서 웹브라우저에 넘겨주었다.