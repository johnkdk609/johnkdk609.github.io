---
layout: post
title: API
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-08-31 21:41:00 +0900
---
스프링 웹 개발의 API 방식에 대해 알아보겠다.

정적 컨텐츠(static content) 방식을 제외하면 두 가지만 기억하면 된다. 이것을 html로 내리는가(MVC와 템플릿 엔진 방식), 아니면 API 방식으로 데이터를 바로 내리는가이다.


## @ResponseBody 문자 반환

우선 HelloController에 하나 더 추가한다.

<img width="716" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7b118dee-8fff-4f36-bf3c-1b84b967458a">

위의 그림에서 @ResponseBody는 html의 바디 태그를 의미하는 것이 아니라, http의 body 부분에 데이터를 직접 넣어주겠다는 것이다. 만약 name에 "spring"이라고 넣으면 이 경우 "hello spring"이라고 나타날 것이다. 즉, 이 문자가 내가 요청한 클라이언트에게 그대로 내려가는 것이다. MVC와 템플릿 엔진 방식과의 차이는 <u>뷰가 없다</u>는 것이다. 그냥 문자가 '그대로' 내려간다.

서버를 실행한 후 localhost:8080/hello-string?name=spring 을 웹브라우저에 입력하면 다음과 같이 나타난다.

<img width="458" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e45a0900-7108-4357-a1cf-639fbe4b343e">

페이지 소스 보기를 하면 다음과 같다.

<img width="612" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4f2b4381-8dc2-489d-9ab4-52e34c4a8c60">

위 사진을 보면 html 태그가 하나도 없고 내가 적은 문자가 그대로 내려간 것을 볼 수 있다.

이전의 템플릿 엔진은 화면을 가지고, 뷰라는 템플릿이 있는 상황에서 조작하는 방식이었고, api 방식은 그대로 데이터를 내려받는 것이다.


## @ResponseBody 객체 반환

만약 이런 단순한 문자가 아니라, 데이터(data)를 내놓으라고 하면 그때 진정으로 API 방식이 진가를 발휘한다.

<img width="1014" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5c768569-f5ee-46d1-99be-58916acfcdbd">

이번에는 객체를 생성하고 객체(hello)를 리턴했다.

(인텔리제이에서 코드를 쓰다가 귀찮으면 cmd + shift + enter을 치면 문장이 완성된다.)

서버를 실행하고 웹브라우저에 localhost:8080/hello-api?name=spring!!! 을 입력하면 다음과 같이 출력된다.

<img width="476" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f85c644a-6576-4fc1-9e94-6657fac8cd29">

페이지 소스 보기를 누르면 다음과 같이 나온다.

<img width="568" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/a4239a29-d9be-455d-86ac-334eaa564f14">

동일하다. 이것은 JSON이라는 방식이다. JSON은 key, value로 이루어진 굉장히 단순한 구조이다. 과거에는 XML 방식이 많이 쓰였었다. 그런데 XML 방식은 열고 닫고 태그를 두 번 써야 하는 등 매우 무겁다. 반면 JSON은 key, value로 바로 나와서 매우 심플하다.

최근에는 거의 JSON 방식으로 통일이 됐다. <b>스프링도 @ResponseBody라고 해놓고 객체를 반환하면 JSON으로 반환하는 것이 기본으로 세팅되어 있다.</b>

참고로 Java의 Getter, Setter의 경우 인텔리제이에서 ctrl + N 을 클릭하면 한 번에 불러올 수 있다. Getter, Setter 방식을 **자바 빈 규약**이라고 한다. 이렇게 해두면 private이기 때문에 외부에서 name을 바로 꺼내쓸 수 없다. 그래서 라이브러리 같은 데에서 쓰거나 이런 메서드를 통해서 접근하게 된다. '프로퍼티 접근 방식'이라 부르기도 한다.


## @ResponseBody 사용 원리

아래는 @ResponseBody의 동작 원리를 간단하게 그림으로 표현한 것이다.

<img width="870" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/d7ff10eb-a00d-4266-8ccb-a72a3f5322e6">

웹브라우저에서 localhost:8080/hello-api를 입력하면 톰캣 내장 서버에서 "hello-api가 왔어" 하면서 스프링에 던진다.

그러면 스프링은 "어 hello-api가 있네, 그런데 @ResponseBody라는 어노테이션이 붙어 있네"라고 판단한다. @ResponseBody가 안 붙어있으면 이전에 알아봤던 템플릿 방식처럼 viewResolver에게 "나에게 맞는 템플릿을 던져줘" 하고 던진다. 그런데 @ResponseBody가 있으면 http 응답에 그대로 데이터를 넘기는 동작을 한다.

만약 문자인 경우 문자 값을 그냥 http 응답에 바로 넣어서 줬다. 그런데 객체가 오는 경우에는 JSON방식으로 데이터를 만들어서 http 응답에 반환하는 것이 기본 정책이다.

@ResponseBody를 보고 HttpMessageConverter가 동작한다. (기존에는 viewResolver가 동작을 했다.)

만약 단순 문자였으면 StringConverter가 동작하고, 객체라면 JsonConverter가 기본으로 동작한다. 객체이기에 JsonConverter가 동작한 경우, hello 객체를 JSON 스타일로 바꾼다. 이 JSON은 바꾼 것을 나를 요청한 웹브라우저에게든, 서버에게든 보내준다.

정리하자면, @ResponseBody가 사용되면,
* <b>HTTP의 BODY에 문자 내용을 직접 반환</b>
* <b>viewResolver 대신에 HttpMessageConverter가 동작</b>
* <b>기본 문자처리: StringHttpMessageConverter</b>
* <b>기본 객체처리: MappingJackson2HttpMessageConverter</b>
* <b>byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있음</b>

이때 Jackson2의 경우 객체를 JSON으로 바꿔주는 대표적인 라이브러리이다. (버전 2)

(참고: 클라이언트의 HTTP Accept 헤더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해서 HttpMessageConverter가 선택된다. 요청을 할 때 '나는 이런 포맷으로 받고 싶어'하는 것이 있는데 그것이 <b>Accept 헤더</b>이다. 거기서 JSON이라고 요청이 오면 JSON을 보낸다. 만약 XML로 받을 것이라고 하면 XML 라이브러리만 꽂아 놓으면 XML Message Converter가 동작한다.)


## 최종 정리

이렇게 세 가지 방식을 알아보았다.

**정적 컨텐츠**는 파일을 그대로 내려주는 것이다.

**MVC와 템플릿 엔진** 방식은 템플릿 엔진을 Model, View, Controller 방식으로 쪼개서 뷰를 템플릿 엔진으로 html을 좀 더 프로그래밍 한 것을 렌더링 하고 렌더링 된 html을 고객에게 전달해주는 것이다.

**API** 방식은 HttpMessageConverter을 통해 JSON으로 바꿔서 반환을 해주는 것이다. 뷰 없이 바로 @ResponseBody를 사용한다.