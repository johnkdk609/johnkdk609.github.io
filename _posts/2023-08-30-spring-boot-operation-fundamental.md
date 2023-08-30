---
layout: post
title: 스프링 부트 동작 원리
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-08-30 23:10:00 +0900
---
스프링 부트의 동작 환경 그림은 다음과 같다

<img width="1000" alt="동작 환경 그림" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/3849d6e5-ba36-4796-952e-98e04fbc369d">

먼저 웹 브라우저에서 localhost:8080/hello 라고 던지면, 톰캣이라는 웹서버를 내장하고 있는 스프링 부트는 서버에서 이것을 받는다. 그러면 '어 /hello네' 하면서 스프링에게 물어본다.

<img width="1000" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/227eb061-4eaa-4916-bd2d-d4e6b34d8858">

그러면 스프링은 위와 같이 helloController의 @GetMapping("hello")을 찾는다. (이때 Get은 http get, post의 그 get이다.) hello URL에 매칭이 된 것이다.

그러면 helloController에 있는 위 메서드가 실행된다. 스프링은 모델(model)이라는 것을 만들어서 넣어준다. 이 모델에다가 addAttribute를 해서 key - "data", value - "hello!!"를 넣어준다. 값(value)은 바뀔 수 있다.

그리고 "hello"를 리턴한다. 이때의 "hello"는 resources:templates/hello.html의 hello이다.

<img width="1000" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/08493cd4-5b58-44d1-87f9-7239ccbbbcb5">

(이때 helloController쪽에서 return "hello";의 "hello"부분을 cmd를 누른 채 클릭하면 hello.html로 이동한다.)

즉, resources:templates 아래에 있는 hello.html을 찾아서 이것을 렌더링하라는 것이다. model을 hello.html에 넘기면서 이 화면을 실행시키라는 것이다.

처리하는 엔진은 Thymeleaf 템플릿 엔진이다. 컨트롤러에서 리턴 값으로 문자를 반환하면 뷰 리졸버(viewResolver)가 화면을 찾아서 처리한다. 뷰 리졸버는 기본적으로 다음과 같이 세팅되어 있다.

> ● 스프링 부트 템플릿엔진 기본적으로 viewName 매핑\
> ● ```resources:templates/``` + {viewName} + ```.html```

자원(resources)에 있는 templates의 viewName, 즉 이 경우에 "hello"를 여는 것이다.

바로 위 그림에서 hello.html을 보면, ```${data}```의 data는 helloController의 model에서의 key값인 것이다. key를 꺼내니까 value인 "hello!!"가 나오는 것이다.

<img width="376" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/21746395-e1c6-40f8-a989-5df9b482913e">

여기서 _hello_ 부분을 클릭하면 다음과 같이 나온다.

<img width="420" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2812c660-16ab-47c3-9d8d-7b3770806ad8">

helloController에서 model의 attributeValue를 "spring!!"이라고 변경하고 저장 및 재실행하면, 다음과 같이 잘 출력되는 것을 알 수 있다.

<img width="416" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/99568bde-9c62-4e8b-a4c9-06218c1fb7cb">