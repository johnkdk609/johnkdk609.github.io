---
layout: post
title: 스프링 빈과 의존관계
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-09-11 17:21:00 +0900
---
이번 시간에는 스프링 빈과 의존관계에 대해 공부할 것이다.

다음과 같이 크게 두 가지 방향을 알아볼 것이다.

* <b>컴포넌트 스캔과 자동 의존관계 설정</b>
* <b>자바 코드로 직접 스프링 빈 등록하기</b>

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

이제 메인 메서드를 실행해보면 문제 없이 잘 실행된다.

<br>

하지만 현재 회원 컨트롤러와 관련된 어떤 기능도 없기 때문에 연결하는 것만 알아본 것이다.

스프링 빈을 등록하는 2가지 방법이 있다.

* <b>컴포넌트 스캔과 자동 의존관계 설정</b>
* <b>자바 코드로 직접 스프링 빈 등록하기</b>

지금까지 한 것은 '컴포넌트 스캔과 자동 의존관계 설정' 방식이다. ```@Controller```, ```@Service```, ```@Repository```를 사용하는 방식이 컴포넌트 스캔 방식이다. 

왜 '컴포넌트 스캔 방식'이냐 하면, 사실 MemberService의 ```@Service```는 ```Component```라고 해야 한다. 그런데 ```cmd```를 누른 채 ```@Service```을 클릭해서 들어가면, 안에 ```@Component```라는 어노테이션이 등록되어 있다.

<img width="892" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c492d059-b920-4440-ab3b-fb64b81fb29b">

```@Controller```, ```@Repository``` 모두 안으로 들어가보면 위와 같이 ```@Component```라고 붙어 있다.

스프링이 올라올 때, ```@Component```와 관련된 어노테이션이 있으면 다 스프링이 객체를 하나씩 생성을 해서 스프링 컨테이너에 등록한다. 이렇게 등록이 되어 있는 것이고, ```@Autowired```는 선을 연결해서 연관 관계를 만들어주는 것이다. MemberController가 MemberService를 쓸 수 있게 해주고, MemberService가 MemberRepository를 쓸 수 있게 되는 것이다.

<br>

그러면, '아무데나 ```@Component```가 있어도 될까?' 하는 의문점이 생길 수 있다. 예를 들어 내가 src/main/java 아래에 demo라는 패키지를 만들고, 그 안에 Demo라는 클래스를 생성한 다음 ```@Service```를 입력해 보겠다.

<img width="859" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/00803175-bef4-4d45-b22a-6bef66c943fe">

이거는 안 된다. 지금 내가 실행시킨 것은 HelloSpringApplication이다.

<img width="291" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/34bb5ac5-704f-4206-83eb-2b5bbabb8732">

HelloSpringApplication부터 시작해서 hello.hellospring패키지를 포함하는 파일들은 자동으로 스프링이 다 뒤져서 스프링 빈으로 등록한다. 그런데 파일 패키지와 동일하지 않은 것은 컴포넌트 스캔을 안 한다. 그래서 Demo클래스는 등록되지 않는다. (물론 어떤 설정을 해주면 될 수 있는데, 기본적으로 컴포넌트 스캔의 대상이 되지 않는다.) hello.hellospring패키지를 포함한, 패키지 하위만 따라 들어가는 것이다.


## 컴포넌트 스캔과 자동 의존관계 설정 정리

<b>컴포넌트 스캔 원리</b>

* ```@Component``` 어노테이션이 있으면 스프링 빈으로 자동 등록된다.
* ```@Controller``` 컨트롤러가 스프링 빈으로 자동 등록된 이유도 컴포넌트 스캔 때문이다.

* ```@Component``` 어노테이션을 포함하는 다음 어노테이션도 스프링 빈으로 자동 등록된다.
    * ```@Controller```
    * ```@Service```
    * ```@Repository```

참고: 생성자에 ```@Autowired```를 사용하면 객체 생성 시점에 스프링 컨테이너에서 해당 스프링 빈을 찾아서 주입한다. 생성자가 1개만 있으면 ```@Autowired```는 생략할 수 있다.

참고: 스프링은 스프링 컨테이너에 스프링 빈을 등록할 때, 기본으로 <b>싱글톤</b>으로 등록한다. (유일하게 하나만 등록해서 공유한다.) 따라서 같은 스프링 빈이면 모두 같은 인스턴스이다. 설정으로 싱글톤이 아니게 설정할 수 있지만, 특별한 경우를 제외하면 대부분 싱글톤을 사용한다.

<br>

## 자바 코드로 직접 스프링 빈 등록하기

이번에는 자바 코드로 직접 스트링 빈을 등록해볼 것이다. ```@Service```, ```@Repository```, ```@Autowired``` 등을 사용하지 않고 하나하나 직접 스프링에 등록하는 방법이다.

먼저, MemberService클래스에 ```@Service```, ```@Autowired``` 어노테이션을 지우고, MemoryMemberRepository에서도 ```@Repository```를 지운다. MemberController는 그대로 둔다.

이제 스프링을 실행해보면, MemberService가 스프링 빈에 등록이 안 돼 있으니 컴포넌트 스캔이 안 되고 오류가 난다.

<img width="1688" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c55cf483-9cd9-4bae-825c-79cf9ece2585">

직접 등록하는 방법이 있다. 일단 hello.hellospring 패키지 아래에 SpringConfig 클래스 파일을 하나 만든다.

<b>SpringConfig클래스</b>의 코드는 다음과 같다.

```java
package hello.hellospring;

import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SpringConfig {

    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
}
```

```@Configuration``` 어노테이션을 위에 붙이고 ```@Bean``` 어노테이션을 붙인다. '내가 스프링 빈을 등록할 거야'라는 의미로 이해하면 된다.

이제 memberService를 생성한다. 이렇게 하면 스프링이 뜰 때 ```@Configuration```을 읽고 '어 이거는 스프링 빈에 등록하라는 것이네'하고 스프링이 인식한다. 그러면서 memberService 로직을 호출해서 스프링 빈에 등록한다.

그런데 ```return new MemberService();```는 보아하니 생성자에서 무언가를 넣어줘야 한다. 빨간줄이 뜨는 소괄호 부분을 클릭하고 ```cmd + P```를 눌러보니 'MemberRepository memberRepository'라고 뜬다. memberRepository를 넣어줘야 하는 것이다.

아래에 또 ```@Bean```을 입력하고 memberRepository를 등록한다. MemberRepository는 인터페이스이고, MemoryMemberRepository는 구현체이다. 이제 위에 ```MemberService();```의 괄호 안에 memberRepository를 넣어준다.

그러면 뜰 때 memberService와 memberRepository를 전부 스프링 빈에 등록하고 스프링 빈에 등록된 memberRepsitory를 MemberService클래스에 넣어준다. 그렇게 아래와 같은 그림이 완성되는 것이다.

<img width="650" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/57505eeb-4032-4b09-b60a-41e09054fac7">

memberService와 memberRepository를 스프링이 올라올 때 ```@Bean```을 보고 컨테이너에 올리는 것이다. 그러면서 memberService는 memberRepository를 사용하도록 했다. 이전에 ```@Autowired``` 하던 것과 비슷하게 스프링 빈에 등록된 memberRepository를 딱 넣어준다.

컨트롤러는 어쩔 수 없다. 컨트롤러는 어쨌든 스프링이 관리하는 것이기 때문에 ```@Controller```를 보고 컴포넌트 스캔에 올라간다. 컴포넌트 스캔이기 때문에 ```@Autowired```를 해주면 된다. 그러면 SpringConfig에 등록한 memberService를 넣어준다.

이제 스프링이 정상적으로 실행된다.

## 자바 코드로 직접 스프링 빈 등록하기 정리

* 참고: 과거에는 자바 코드로 설정하지 않고 <b>XML</b>이라는 문서로 설정을 했다. 지금은 XML을 잘 사용하지 않고 거의 자바 코드로 설정한다.

<br>

* 참고: DI에는 필드 주입, setter 주입, 생성자 주입 이렇게 3가지 방법이 있다. 의존관계가 실행중에 동적으로 변하는 경우는 거의 없으므로 <b>생성자 주입</b>을 권장한다. (위에서 사용한 방법이 생성자 주입 방식이다.)

필드 주입의 경우, MemberController에서 생성자를 빼고, 필드에다가 ```@Autowired```를 하는 것이다.

<img width="602" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/302c8861-8ae8-4128-bf7c-6dd3601b15fb">

그런데 ```@Autowired``` 부분에 물결이 쳐져 있다. 그 부분을 클릭하고 ```option + Enter```를 누르면 다음과 같이 뜬다.

<img width="1027" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0c67f70a-e0d4-4749-8c4b-f63a955941a5">

'Create Constructor' 즉 생성자를 주입하라는 것이다. 필드 주입은 별로 좋지 않다. 왜냐하면 필드 주입은 무언가 바꿀 수 있는 방법이 없다. 스프링 뜰 때만 넣어주고 중간에 바꿀 수 있는 방법이 없는 것이다.

setter 주입의 경우, 누군가가 MemberController를 호출했을 때 이것이 public으로 열려 있어야 한다. 중간에 잘못 바꾸면 문제가 생길 수 있다. 어플리케이션 로딩 시점에 바꾸는 것이지, 한 번 세팅이 되면 바꿀 일이 없기 때문이다.

<br>

* 참고: 실무에서는 주로 정형화된 컨트롤러, 서비스, 리포지토리 같은 코드는 컴포넌트 스캔을 사용한다. 그리고 정형화 되지 않거나, 상황에 따라 구현 클래스를 변경해야 하면 설정을 통해 스프링 빈으로 등록한다.

'정형화된 코드'란 일반적으로 우리가 작성하는 컨트롤러, 서비스, 리포지토리 등을 말한다. 중요한 부분은 상황에 따라 구현 클래스를 변경해야 한다는 것이다.

MemberRepository를 설계할 때 '아직 데이터 저장소가 선정되지 않았다'는 가상의 시나리오가 있었다.

<img width="698" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f3d984f7-5b02-4ea2-9be5-f923a7ac3db0">

일단 메모리로 만들고 나중에 교체하기로 했었다. 그래서 위와 같이 인터페이스를 설계하고 구현체로 MemoryMemberRepository를 사용하는 그림이 된 것이었다. 그런데 나중에 이 MemoryMemberRepository를 다른 리포지토리로 바꿔치기할 것이다. 기존에 운영 중인 코드를 하나도 손 대지 않고 바꿔치기할 수 있는 방법이 있다.

MemoryMemberRepository를 데이터베이스에 실제로 연결하는 리포지토리로 바꿀 것인데, 기존 MemberService나 나머지 코드에 일절 손 대는 것 없이 바꿔치기할 수 있는 것이다. 그것을 하려면 구현체를 바꿔치기 해야 한다.

나중에 데이터베이스에 연결하게 되면 다른 코드를 전혀 손 댈 것 없이 MemoryMemberRepository를 DbMemberRepository로 바꿔주기만 하면 된다.

이것이 직접 설정 파일을 운영할 때의 장점이다. 컴포넌트 스캔을 사용하면 여러 코드를 바꿔야 하는데, 이 방법은 설정 파일만 조금 수정하면 된다.

<br>

* 주의: ```@Autowired```를 통한 DI는 ```helloController```, ```memberService``` 등과 같이 스프링이 관리하는 객체에서만 동작한다. 스프링 빈으로 등록하지 않고 내가 직접 생성한 객체에서는 동작하지 않는다.