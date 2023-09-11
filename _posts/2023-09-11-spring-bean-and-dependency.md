---
layout: post
title: 스프링 빈과 의존관계
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-09-11 17:21:00 +0900
---
이번 시간에는 스프링 빈과 의존관계에 대해 공부할 것이다.

다음과 같이 크게 두 가지 방향을 알아볼 것이다.

* 컴포넌트 스캔과 자동 의존관계 설정
* 자바 코드로 직접 스프링 빈 등록하기

## 컴포넌트 스캔과 자동 의존관계 설정

여태까지 Member 서비스를 통해서 멤버에 가입할 수 있고, 리포지토리에 저장이 되고, 리포지토리에서 꺼내올 수 있는 로직들을 만들었다. 그리고 테스트도 수행했다. 이번에는 화면을 붙이고 싶다. 그러려면 컨트롤러와 뷰 템플릿이 필요하다. 회원가입을 하고, 회원가입된 결과를 html로 뿌려주는 것들을 하려 하는 것이다.

그러려면 멤버 컨트롤러를 만들어야 한다. 이 멤버 컨트롤러가 멤버 서비스를 통해서 회원가입 하고 멤버 서비스를 통해서 데이터를 조회할 수 있어야 한다. 이렇게 되는 것을 "의존관계가 있다"라고 표현한다. ('멤버 컨트롤러가 멤버 서비스에 의존한다'고 표현한다.)

main/java/hello.hellospring/controller 아래에 MemberController클래스를 생성한다.

<img width="270" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/49dc7795-a5e7-4890-a0ff-2a9c7bdc5903">

그리고 ```@Controller``` 어노테이션을 입력한다. 이렇게까지 해두면 스프링이 처음에 뜰 때 '스프링 컨테이너'라는 스프링 통이 생긴다. 거기에 컨트롤러 어노테이션이 있으면 멤버 컨트롤러 객체를 생성해서 스프링에 넣어둔다. 그리고 스프링이 관리를 한다.

이전에 생성했던 HelloController를 예시로 설명을 하자면, ```@Controller```라는 어노테이션을 보고 스프링이 뜰 때 HelloController를 객체를 생성해서 스프링이 들고 온다. 이것을 스프링 컨테이너에서 "<b>스프링 빈이 관리된다</b>"고 표현한다.

<img width="700" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ad86f063-44b6-4f60-ab65-b6ce4c07028f">

위 그림을 보면, 스프링 부트 안에 스프링 컨테이너와 내장 톰캣 서버가 있다. 스프링 컨테이너에 잘 보면 helloController가 녹색 원 안에 있다. (저자는 이 녹색 원이 마치 Bean, 콩을 표현하려 했다고 한다.)

```@Controller```라는 어노테이션이 있으면 스프링이 뜰 때 생성을 해서 자기가 관리를 하는 것이다. 그래서 스프링과 관련된 컨트롤러 기능이 동작하는 것이다.

<br>

<b>MemberController</b>의 코드는 다음과 같다.

```java
package hello.hellospring.controller;

import hello.hellospring.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;

@Controller
public class MemberController {

    private final MemberService memberService;

    @Autowired
    public MemberController(MemberService memberService) {
        this.memberService = memberService;
    }
}
```

```private final MemberService memberService = new MemberService();```로 입력할 수도 있다. 그런데, <b>스프링이 관리를 하게 되면 다 스프링 컨테이너에 등록하고 스프링 컨테이너로부터 받아서 쓰도록 바꿔야 한다.</b> 

이렇게 new로 해서 사용하면 MemberController 말고 다른 여러 컨트롤러들이 memberService를 가져다 쓸 수 있다. 예를 들어 주문 컨트롤러에서도 memberService를 가져다 쓸 수 있는 것이다. 이 객체를 new로 했는데, new 뒤에 MemberService 부분을 ```cmd```를 누른 채 클릭해서 들어가면 별 기능이 없다. 이것은 여러 개의 인스턴스를 생성할 필요가 없는 것이다.

이제부터는 스프링 컨테이너에 등록하고 쓸 것이다. 스프링 컨테이너에 등록하면 딱 하나만 등록이 된다. 그리고 그 외에 굉장히 부가적인 여러 가지 효과를 볼 수 있다. (나중에 알게 될 것이다.)

그래서 ```private final MemberService memberService;```로 수정하고, ```cmd + N```을 누른 다음 Constructor(생성자)를 클릭해서 생성한다. 그리고 그 위에 ```@Autowired``` 어노테이션을 입력한다.

스프링 컨테이너가 뜰 때 MemberController가 생성이 되는데, 그때 이 생성자를 호출한다. 그런데 생성자에 ```@Autowired``` 어노테이션이 붙어있으면 memberService를 스프링이 스프링 컨테이너에 있는 MemberService를 가져다가 연결을 시켜준다.

<img width="539" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f69fbae0-31cd-4cc5-9667-0a2151ac24ef">

그런데 위 그림을 보면 memberService부분에 빨간 줄이 뜬다. (이 빨간 줄은 IntelliJ IDE가 해주는 것이다.)

스프링을 실행해 보면 다음과 같이 오류가 나타난다.

<img width="1401" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1dae7cd0-3f5f-4ac1-a411-864afdf63940">

'required a bean of type 'hello.hellospring.service.MemberService' that could not be found.'라는 메세지가 뜬다.

```@Autowired```라고 입력해두면 스프링 컨테이너에서 MemberService를 가져온다고 했다.

<img width="577" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f8d67981-17d3-46b1-81a0-a2e61dc73886">

위의 그림을 보면, helloController는 스프링이 뜰 때 스프링 컨테이너에 등록이 된다. ```@Autowired```라고 되어 있으면 스프링 컨테이너에서 관리하는 MemberService를 가져다가 스프링이 넣어준다. (연결을 시켜주는 것이다. 그래서 wired이다.) 그런데 안 된다.

MemberService클래스를 열어보면, 얘는 순수한 자바 클래스이다. 스프링이 얘를 알 수 있는 방법이 없다. MemberController은 ```@Controller``` 어노테이션을 보고 스프링이 '아 얘는 내가 관리하는 애구나'하고 생성을 하는 규칙이 있다. 하지만 MemberService의 경우 순수한 자바 코드이다. 그래서 아무것도 안 된다.

이 문제를 해결하려면 MemberService에 ```@Service``` 어노테이션을 입력해주어야 한다.

<img width="254" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/93cc8e0f-ea5e-4f1c-8f7c-d81bbcc9b4b2">

```@Service```를 붙이면 스프링이 올라올 때 MemberService를 보고 '어, 서비스네'하고 스프링 컨테이너에 MemberService를 등록한다.

<br>

이제 리포지토리를 볼 것이다. MemoryMemberRepository클래스로 가서 윗부분에 ```@Repository``` 어노테이션을 붙여주면 된다.

<img width="546" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/6a1b2394-9b4f-448d-8534-8ed23e1c2f58">

<br>

이렇게 <b>컨트롤러-서비스-리포지토리</b>가 매우 정형화된 패턴이다. 컨트롤러를 통해서 외부 요청을 받고, 서비스에서 비즈니스 로직을 만들고, 리포지토리에서 데이터를 저장하는 것이 정형화된 패턴인 것이다.

이렇게 해두면 스프링이 스프링이 뜰 때 컨트롤러, 서비스, 리포지토리를 한 번에 가지고 올라온다. 

<img width="650" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/57505eeb-4032-4b09-b60a-41e09054fac7">

위 그림을 보면, 컨트롤러와 서비스를 연결시켜 주어야 한다.

연결시켜줄 때에는 ```@Autowired```를 쓰면 된다. 컨트롤러의 생성자에서 ```@Autowired```를 쓰면 MemberController가 생성이 될 때 스프링 빈에 등록되어 있는 MemberService객체를 가져다가 넣어준다. 이게 바로 '<b>Dependency Injection(의존성 주입)</b>'이다.

MemberService는 MemberRepository가 필요하다. 스프링이 뜰 때 '어 서비스네' 하고 스프링 컨테이너에 등록하면서 생성자를 호출한다. 이때 ```@Autowired```가 있으면 '아 너는 MemberRepository가 필요하구나' 하고 스프링 컨테이너에 MemberRepository를 딱 넣어준다. 지금 같은 경우 MemoryMemberRepository가 구현체로 있으니 이것을 서비스에 주입해준다.

위 그림처럼 helloController, memberController, memberRepository가 딱 연결이 되는 것이다.

<br>

이제 메인 메서드를 실행해보면 문제 없이 잘 실행된다.