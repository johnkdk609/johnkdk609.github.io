---
layout: post
title: 정적 컨텐츠
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-08-31 12:14:00 +0900
---
웹을 개발한다는 것은 크게 3가지 방법이 있다.

1. **정적 컨텐츠**
2. **MVC와 템플릿 엔진**
3. **API**

첫 번째는 정적 컨텐츠(static content)이다. 앞서 알아본 Welcome Page처럼 서버에서 무엇 하는 것 없이 파일을 그대로 웹브라우저에 띄워주는 것이다.

두 번째는 MVC와 템플릿 엔진 방식으로 가장 많이 쓰이는 방법이다. 과거에 JSP, PHP 등이 템플릿 엔진으로, html을 그냥 주는 것이 아니라 서버에서 프로그래밍을 해서 html을 동적으로 바꿔서 내리는 것이다. 이것을 하기 위해 모델, 템플릿 엔진 화면 그리고 컨트롤러 이 세 가지를 Model, View, Controller 즉 MVC라고 한다. 정적 컨텐츠는 파일을 그대로 웹브라우저에 전달하는 것이라면, MVC와 템플릿 엔진은 서버에서 좀 변형을 해서 내려주는 방식이라고 차이점을 이해하면 된다. 

세 번째는 API이다. 만약 안드로이드나 아이폰 클라이언트와 개발을 해야 하는 경우 서버 입장에서는 요즘은 JSON이라는 데이터 구조 포맷으로 클라이언트에게 데이터를 전달하는 방식을 API 방식이라고 한다. (과거에는 XML 포맷을 사용했다.) Vue.js, React 등을 쓸 때에도 API로 데이터만 내려주면 화면은 클라이언트가 알아서 그리고 정리하는 방식으로 사용되기도 한다. 서버끼리 통신할 때에도 API 방식이 활용된다. (서버끼리 통신할 때에는 html을 내릴 필요가 없다. 어떤 데이터가 왔다갔다 하는지가 중요하기 때문.)


## 정적 컨텐츠

스프링 부트는 정적 컨텐츠 기능을 자동으로 제공한다.

<img width="948" alt="https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/72e77960-9590-45a3-82d2-898f4521855f">

우선, resources:static 안에 hello-static.html을 만들고 내용을 작성한다.

<img width="1051" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e60e8283-46da-47dc-878f-7dd046bed816">

그리고 서버를 실행시킨 다음, 웹브라우저에 localhost:8080/hello-static.html을 입력하면 다음과 같이 나타난다.

<img width="388" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ebda4b3e-df20-4575-912a-c4a58307d736">

해당 화면에서 페이지 소스 보기를 누르면 다음과 같이 나타난다.

<img width="672" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c8a83400-812b-446b-abff-cedb3f587f8e">


## 정적 컨텐츠의 원리

정적 컨텐츠의 개괄적인 이미지는 다음과 같다.

<img width="873" alt="static-content image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5e57f2df-f4f8-4f16-83ca-35dcf235ddde">

웹브라우저에 localhost:8080/hello-static.html을 입력하면 일단 내장 톰캣 서버가 요청을 받는다. "어, hello-static.html이라고 왔대" 하면서 스프링에 넘긴다.

그러면 스프링은 먼저 컨트롤러(Controller) 쪽에서 hello-static이 있는지 찾아본다. **컨트롤러가 우선순위를 가지는 것이다.** 그런데 hello-static과 매핑된 컨트롤러는 없다.

그 다음에 스프링 부트는 resources 안에 있는 static/hello-static.html을 찾는다. 여기에 있으니 반환을 하는 것이다.