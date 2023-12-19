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


## 2. 싱글톤 패턴

이번에는 싱글톤 패턴에 대해 알아볼 것이다.

<b>싱글톤 패턴이란 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴</b>이다. 한 JVM(자바 서버) 안에서 객체 인스턴스가 하나만 생성되는 것으로, 절대 두 개가 생성되지 않도록 만드는 디자인 패턴이다.

결론은 똑같은 객체 타입의 인스턴스를 2개 이상 생성하지 못하도록 막으면 된다. (private 생성자를 사용해서 외부에서 임의로 new 키워드를 사용하지 못하도록 막아야 한다.)

<br>

코드로 만들어 보겠다. test 밑에 singleton 패키지에 SingletonService클래스를 생성한다. <b>SingletonService클래스</b>의 코드는 다음과 같다.

```java
package hello.core.singleton;

public class SingletonService {

    private static final SingletonService instance = new SingletonService();

    public static SingletonService getInstance() {
        return instance;
    }

    private SingletonService() {
    }

    public void logic() {
        System.out.println("싱글톤 객체 로직 호출");
    }
    
}
```

먼저 ```private static final SingletonService instance = new SingletonService();```를 입력해 자기자신을 선언한다. 관례상 instance를 많이 사용한다. 자기 자신을 내부에 private으로 하나 가지고 있는데, static으로 가지고 있다. 이렇게 하면 클래스 레벨에 올라가기 때문에 딱 하나만 올라가게 된다. 이제 얘를 조회해야 한다. ```public static SingletonService getInstance()```를 기입하고, instance를 리턴하게 한다. 

JVM이 뜰 때 SingletonService의 static 영역에 ```new SingletonService();```라고 되어 있으니 내부적으로 실행해서 객체를 생성한 다음 ```instance```에 참조를 넣어 놓는다. 그러면 객체 인스턴스를 딱 하나 생성해서 이 안에 들어가 있는 것이다.

그리고 private 생성자를 사용한다. private은 자기 자신을 호출할 수 있다. 가령 다른 클래스인 SingletonTest에서 다음과 같이 SingletonService을 만들려 하면 다음과 같이 안 된다.

<img width="1085" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f4b45f1b-369b-4324-9f09-844e3ef33dc3">

위와 같이 'private access' 여서 안 된다고 나온다. 컴파일 오류가 나는 것이다.

그리고 로직이 하나 있어야 하니, 위와 같이 ```public void logic()```을 기입한다. 

이렇게 하면 완벽한 싱글톤이 된다. 자바가 뜨면서 static 영역에 있는 SingletonService를 초기화하면서 new를 한 번 생성해서 가지고 있다. 이 instance에 참조를 꺼낼 수 있는 방법은 getInstance밖에 없다. 그리고 SingletonService를 생성할 수 있는 곳은 아무 곳도 없다.

<br>

정리하자면 이러하다.

1. static영역에 객체 instance를 미리 하나 생성해서 올려둔다.
2. 이 객체 인스턴스가 필요하면 오직 ```getInstance()```메서드를 통해서만 조회할 수 있다. 이 메서드를 호출하면 항상 같은 인스턴스를 반환한다.
3. 딱 1개의 객체 인스턴스만 존재해야 하므로, 생성자를 private으로 막아서 혹시라도 외부에서 new 키워드로 객체 인스턴스가 생성되는 것을 막는다.

제일 좋은 오류는 Compile Error이다. 진짜 잘 설계한 객체는 컴파일 오류만으로 웬만한 오류가 다 잡히도록 설계하는 것이다. 이렇게 private으로 막아버리는 것이다. 누군가가 이것을 보고 '왜 private이지?' 하고 한 번 생각하다가, 싱글톤이라는 것을 생각하게 될 것이다. 만약 public으로 돼 있으면 '생성해서 쓰는 것인가보다' 하고 생성해서 2~3개가 되어버릴 수 있는 것이다.

<br>

이제 테스트를 하나 만들어볼 것이다.