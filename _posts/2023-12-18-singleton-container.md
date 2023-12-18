---
layout: post
title: 싱글톤 컨테이너
description: 스프링 핵심 원리 - 기본편
categories: Spring-SpringBoot
date: 2023-12-18 15:12:00 +0900
---
이번에는 싱글톤 컨테이너(Singleton Container)에 대해 알아보겠다.

객체 인스턴스가 현재 나의 JVM에 하나만 있는 패턴을 말한다.

<br>

## 목차

1. 웹 애플리케이션과 싱글톤
2. 싱글톤 패턴
3. 싱글톤 컨테이너
4. 싱글톤 방식의 주의점
5. @Configuration과 싱글톤
6. @Configuration과 바이트코드 조작의 마법

첫 번째 시간에는 웹 애플리케이션에서 왜 싱글톤이 많이 사용되는지 그 이유들에 대해 알아볼 것이다. 그리고 싱글톤 패턴에 대해 간단히 알아보고, 싱글톤 컨테이너, 싱글톤 방식의 주의점에 대해 쭉 알아볼 것이다. 그리고 스프링이 제공하는 ```@Configuration```의 비밀에 대해서 파헤쳐볼 것이다.

<br>

## 1. 웹 애플리케이션과 싱글톤

스프링은 태생이 기업용 온라인 서비스 기술을 지원하기 위해 탄생했다. 애플리케이션의 종류가 굉장히 많다. 대부분의 스프링 애플리케이션은 웹 애플리케이션이다. 물론 웹이 아닌 애플리케이션도 스프링으로 얼마든지 개발할 수 있다. (배치 애플리케이션, 데몬 애플리케이션 등..) 웹 애플리케이션은 보통 여러 고객이 동시에 요청을 한다.

<img width="992" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/32feb88e-de8a-4322-95fd-efe7fad9d0e7">

위 그림을 보면, 클라이언트 A, B, C가 memberService를 스프링한테 요청한다. 세 명이 동시에 요청하면, 우리가 직접 만들었던 DI컨테이너(AppConfig)의 경우 다음과 같이 동작한다.

<img width="714" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4c86543b-257b-42d9-8e01-350996eeb304">

위와 같이 AppConfig를 봤을 때, memberService를 달라고 하면 new 해서 MemberServiceImpl를 객체로 생성해서 반환한다. 그렇게 해서 클라이언트 A에게 반환한다. 클라이언트 B가 똑같이 요청하면, AppConfig는 또 new MemberService 해서 반환해준다. 클라이언트 C가 요청해도 마찬가지이다.

고객이 세 번 요청하면 객체가 세 개 생성되는 것이다. 웹 애플리케이션은 고객이 계속 요청을 하는 애플리케이션이다. 이런 경우, 계속 요청이 올 때마다 객체를 만들어야 한다는 문제가 있다.

<br>

먼저, 스프링이 없는 순수한 DI 컨테이너를 한 번 만들어서 테스트 해보겠다.

테스트 쪽에 singleton이라는 패키지를 생성하고, 그 안에 SingletonTest클래스를 생성한다. <b>SingletonTest클래스</b>의 코드는 다음과 같다.

```java
package hello.core.singleton;

import hello.core.AppConfig;
import hello.core.member.MemberService;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

public class SingletonTest {

    @Test
    @DisplayName("스프링 없는 순수한 DI 컨테이너")
    void pureContainer() {
        AppConfig appConfig = new AppConfig();
        // 1. 조회: 호출할 때마다 객체를 생성
        MemberService memberService1 = appConfig.memberService();
        
        // 2. 조회: 호출할 때마다 객체를 생성
        MemberService memberService2 = appConfig.memberService();
        
        // 참조값이 다른 것을 확인
        System.out.println("memberService1 = " + memberService1);
        System.out.println("memberService2 = " + memberService2);
    }
}
```

내가 만들었던 순수한 DI 컨테이너가 가진 문제점을 볼 것이다. 그리고 나중에 스프링과 비교를 해볼 것이다. 먼저 AppConfig가 필요하니 ```new AppConfig```로 만든다. 그리고 memberService1, memberService2를 만든다. 그리고 참조값이 다른 것을 확인해보겠다.

테스트를 실행하면 다음과 같이 나온다.

<img width="1175" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/53d6e6ab-e1b6-4faa-b40b-254561f6d21d">

appConfig에 memberService를 달라고 할 때마다 다른 것이 생성된다. 1번은 @7a8c8dcf, 2번은 @24269709 이렇게 나오는 것이다. 이렇게 되면 JVM 메모리에 계속 객체가 생성되어서 올라갈 것이다.

스프링은 웹 애플리케이션을 주로 만드는데, 웹 애플리케이션의 특징은 고객의 요청이 참 많다는 것이다. 이렇게 매번 객체를 만들게 되면 매우 효율적이지 않다.

<br>

이제 테스트에 다음과 같은 코드를 추가한다. 테스트 검증을 하기 위해서이다. (눈으로 확인하기는 했지만, 테스트는 자동화 되게 만들어야 한다.)

```java
// memberService1 ≠ memberService2
Assertions.assertThat(memberService1).isNotSameAs(memberService2);
```

이제 실행하면 다음과 같이 성공적으로 테스트가 돌아간다.

<img width="1089" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ed74bda1-1f66-45ed-ad5c-28e781d864e9">

호출을 할 때마가 객체를 새로 생성하고 있는 것이다. 사실 MemberServiceImpl만 새로 생성하고 있는 것이 아니라, memberRepository까지 새로 생성하고 있어서 사실상 객체가 총 4개 생성된 것이다.

<br>

정리하자면 다음과 같다.

* 우리가 만들었던 스프링 없는 순수한 DI 컨테이너인 AppConfig는 요청을 할 때마다 객체를 새로 생성한다.
* 고객 트래픽이 초당 100이 나오면 초당 100개 객체가 생성되고 소멸된다! → 메모리 낭비가 심하다.
* 해결방안은 해당 객체가 딱 1개만 생성되고, 공유하도록 설계하면 된다. → <b>싱글톤 패턴</b>