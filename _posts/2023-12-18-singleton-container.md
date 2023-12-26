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

이제 테스트를 하나 만들어볼 것이다. 그냥 이전에 만들었던 SingletonTest클래스에 만들 것이다.

테스트 코드는 다음과 같다.

```java
@Test
@DisplayName("싱글톤 패턴을 적용한 객체 사용")
void singletonServiceTest() {
    SingletonService singletonService1 = SingletonService.getInstance();
    SingletonService singletonService2 = SingletonService.getInstance();

    System.out.println("singletonService1 = " + singletonService1);
    System.out.println("singletonService2 = " + singletonService2);

    assertThat(singletonService1).isSameAs(singletonService2);
}
```

먼저 new 해서 싱글톤 서비스를 생성하려고 하면 안 된다. private access 때문이다. 그래서 조회하기 위해서는 ```SingletonService.getInstance()```의 방법을 사용해야 한다. singletonService1과 singletonService2를 만든다. 호출할 때마다 객체를 생성하는지 안 하는지 보기 위해서 참조값을 확인해볼 것이다. 위와 같이 입력하고 출력해보면 다음과 같이 같은 객체 인스턴스가 반환된 것을 확인할 수 있다.

<img width="1178" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1db6853a-e1f3-40c1-a84c-7286692bb4b5">

이미 자바가 뜰 때 생성해놓은 것을 가져다 쓰는 것이다. 객체 인스턴스를 생성하는 데 드는 비용이 1,000 정도라면, 이미 생성한 것을 가져오는 비용은 1 정도라고 생각하면 된다. 그리고 검증을 하기 위해 Assertions를 static import를 해주고, ```assertThat(singletonService1).isSameAs(singletonService2);```를 입력한다. ```isSameAs```와 ```isEqualTo```가 있는데, Same은 참조를 비교하기 위해 ```==```를 하는 것이고, Equals는 자바의 ```equal```이다. 여기서는 진짜 인스턴스가 같은지 비교하는 것이기 때문에 ```isSameAs```를 사용하면 된다.

<br>

이렇게 해서 싱글톤 패턴을 구현하는 것을 알아봤다. 이제 기존에 만들어둔 AppConfig.java를 다 싱글톤 패턴을 넣어 바꾼 다음, ```getInstance```해서 반환하도록 하면 된다. 그러면 싱글톤이 다 적용된다.

그런데 그렇게 할 필요가 없다. 왜냐하면 스프링 컨테이너를 쓰면, 스프링 컨테이너가 기본적으로 객체를 다 싱글톤으로 만들어서 관리해주기 때문이다. 이 싱글톤 패턴이 적용되면, 고객의 요청이 초당 100개가 와서 초당 100개 요청을 처리해야 해도, 객체를 하나도 안 만든다. 있는 객체를 그대로 재활용하는 것이다. 성능이 상당히 좋아질 것이다.

<br>

정리하면 다음과 같다.

* private으로 new 키워드를 막아놨다.
* 호출할 때마다 같은 객체 인스턴스를 반환하는 것을 확인할 수 있다.

(참고: 싱글톤 패턴을 구현하는 방법은 여러가지가 있다. 여기서는 객체를 미리 생성해두는 가장 단순하고 안전한 방법을 선택했다.)

싱글톤 패턴을 적용하면 고객의 요청이 올 때마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 효율적으로 사용할 수 있다. 하지만 싱글톤 패턴은 다음과 같은 수많은 문제점들을 가지고 있다.

<br>

<b>싱글톤 패턴 문제점</b>

* 싱글톤 패턴을 구현하는 코드 자체가 많이 들어간다.

이전에 만들었던 SingletonService클래스 코드를 생각하면 된다.

<img width="691" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/849afb41-0e91-4da1-ae20-edad89ec53e2">

위와 같은 코드를 넣어야 하고, 이것이 상당히 큰 단점이라고 할 수 있다.

* 의존관계상 클라이언트가 구체 클래스에 의존한다. → DIP를 위반한다.

위의 그림을 봤을 때, getInstance() 한 것을 불러와야 하는 문제가 있다. 예를 들어서 AppConfig에서 이것을 할려 그러면, ```memberServiceImpl.getInstance()```의 방식으로 꺼내야 한다. 다른 애들도 클라이언트가 ```구체클래스.getInstance()```의 방식으로 꺼내게 된다. 그러니까 구체 클래스에 의존하게 되고 DIP를 위반하는 것이다.

* 클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높다.
* 테스트하기 어렵다.

싱글톤은 내가 딱 지정해서 가져온다. 인스턴스를 미리 받아놔서 설정이 끝난 것이다. 유연성이 떨어진다.

* 내부 속성을 변경하거나 초기화하기 어렵다.
* private 생성자로 자식 클래스를 만들기 어렵다.
* 결론적으로 유연성이 떨어진다.

Dependency Injection과 같은 것을 적용하기 어렵다. 왜냐하면 ```구체클래스.getInstance()```와 같은 것을 해줘야 하기 때문이다.

* 안티패턴으로 불리기도 한다.

<br>

물론 장점도 있다. share을 할 수 있고, 확실하게 객체 하나가 있다는 것이 보장된다. 하지만 위와 같은 수많은 단점들이 있기도 하다.

그런데 스프링 프레임워크는 싱글톤이 가진 단점은 다 제거하면서 객체를 싱글톤으로 관리해준다.


## 3. 싱글톤 컨테이너

이제 싱글톤 컨테이너에 대해 알아보겠다.

스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하면서, 객체 인스턴스를 싱글톤(1개만 생성)으로 관리한다. 지금까지 우리가 학습한 스프링 빈이 바로 싱글톤으로 관리되는 빈이다.

### 싱글톤 컨테이너

* 스프링 컨테이너는 싱글톤 패턴을 적용하지 않아도, 객체 인스턴스를 싱글톤으로 관리한다.
    * 이전에 설명한 컨테이너 생성 과정을 자세히 보자. 컨테이너는 객체를 하나만 생성해서 관리한다.

<img width="990" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f094c174-04ff-49e1-a65f-65eafe4dfb33">

이전에 했던 스프링 빈 등록에 대한 그림이다. ```@Bean```이 붙어있는 것들을 다 호출해서 빈 객체를 미리 등록해서 관리해준다. 객체 인스턴스를 미리 생성해서 관리해주는 것이다. 그러면 조회하면 관리되는 오른쪽의 객체들을 조회해준다. 두 번 조회하면 이미 관리되던 것과 똑같은 것을 조회해주는 것이다.

* 스프링 컨테이너는 싱글톤 컨테이너 역할을 한다. 이렇게 싱글톤 객체를 생성하고 관리하는 기능을 싱글톤 레지스트리라 한다.
* 스프링 컨테이너의 이런 기능 덕분에 싱글톤 패턴의 모든 단점을 해결하면서 객체를 싱글톤으로 유지할 수 있다.
    * 싱글톤 패턴을 위한 지저분한 코드가 들어가지 않아도 된다.
    * DIP, OCP, 테스트, private 생성자로부터 자유롭게 싱글톤을 사용할 수 있다. → 유연성을 늘릴 수 있는 것이다.

<br>

그러면 테스트를 해보겠다. 기존에 사용하던 SingletonTest클래스에 그대로 코드를 추가하겠다. 테스트 코드는 다음과 같다.

```java
@Test
@DisplayName("스프링 컨테이너와 싱글톤")
void springContainer() {
//        AppConfig appConfig = new AppConfig();
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    MemberService memberService1 = ac.getBean("memberService", MemberService.class);
    MemberService memberService2 = ac.getBean("memberService", MemberService.class);

    // 참조값이 다른 것을 확인
    System.out.println("memberService1 = " + memberService1);
    System.out.println("memberService2 = " + memberService2);

    // memberService1 ≠ memberService
    assertThat(memberService1).isSameAs(memberService2);
}
```

이전에 pureContainer의 테스트 코드를 복사 붙이기 하고, 이번에는 ```new AnnotationConfigApplicationContext(AppConfig.class);```를 입력해 ac를 생성한다. 그리고 ```ac.getBean("memberService", MemberService.class);```를 두 번씩 해서 두 번 조회한다. 그리고 아래에서 ```assertThat(memberService1).isSameAs(memberService2);```로 ```isSameAs```를 사용한다. 이제 실행하면 다음과 같이 성공적으로 수행된다.

<img width="1684" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c60efd9d-62c4-44e0-a13b-3ed24f9733f5">

보면 조회할 때마다 스프링이 처음에 컨테이너에 등록한 빈을 계속 반환해주는 것이다. 싱글톤이 맞다.

MemberServiceImpl클래스 코드에 들어가 보면, 싱글톤 패턴과 관련한 코드가 하나도 없다.

<img width="793" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/6dd090ea-79a2-4a31-88c7-1190c98ff98d">

어차피 스프링을 쓰면 스프링 컨테이너를 통해서 사용하기 때문에, 직접 new로 생성하거나 하지는 않는다. 스프링 컨테이너에서 꺼내서 사용해야 된다는 내부 규약을 가지고 개발하면 되는 것이다.

그리고 스프링 컨테이너가 엮이면 이미 주입하고 하는 것은 다 같은 싱글톤이기 때문에 그런 걱정은 없다.

<br>

그래서 싱글톤 컨테이너를 적용하면 다음과 같이 된다.

<img width="991" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1ed34b17-63a2-457d-a711-6aae004dda89">

고객인 클라이언트 A, B, C가 요청할 때마다 동일한 memberService를 반환해준다. 스프링 컨테이너 덕분에 고객의 요청이 올 때마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 효율적으로 재사용할 수 있는 것이다.

(참고: 스프링의 기본 빈 등록 방식은 싱글톤이지만, 싱글톤 방식만 지원하는 것은 아니다. 요청할 때마다 새로운 객체를 생성해서 반환하는 기능도 제공한다. 자세한 내용은 뒤에 빈 스코프에서 알아볼 것이다.)


## 4. 싱글톤 방식의 주의점

이번에는 싱글톤 방식의 주의점에 대해 알아볼 것이다.

싱글톤 패턴이든, 스프링 같은 싱글톤 컨테이너를 사용하든, 객체 인스턴스를 하나만 생성해서 공유하는 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지(stateful)하게 설계하면 안 된다.

<b>무상태(stateless)로 설계해야</b> 한다!
    * 특정 클라이언트에 의존적인 필드가 있으면 안 된다.
    * <b>특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안 된다.</b>
    * 가급적 읽기만 가능해야 한다.
    * 필드 대신에 자바에서 공유되지 않는, 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.

스프링 빈의 필드에 공유 값을 설정하면 정말 큰 장애가 발생할 수 있다.

<br>

상태를 유지할 경우 발생하는 문제점 예시를 알아보겠다. 테스트 쪽의 singleton패키지에 StatefulService클래스를 생성한다. <b>StatefulService클래스</b>의 코드는 다음과 같다.

```java
package hello.core.singleton;

public class StatefulService {

    private int price;  // 상태를 유지하는 필드

    public void order(String name, int price) {
        System.out.println("name = " + name + " price = " + price);
        this.price = price; // 여기가 문제!
    }

    public int getPrice() {
        return price;
    }
}
```

어떤 서비스가 있는데, 이름이 StatefulService인 것이다. 가격이란 필드를 가지고 있다. 그리고 여기에는 order하는 로직이 있다. 주문할 때 가격을 저장하게 하는 것인데, ```this.price = price;```를 추가한다. 이 부분이 문제가 된다. 클라이언트 의도는 주문을 해서 값을 여기에 저장해두고, 뒤에 ```public int getPrice()```로 꺼내고 싶었던 것이다.

<br>

이제 테스트 코드를 봐야 한다. 테스트 만들 때, StatefulService를 클릭해두고, ```cmd + shift + T```를 클릭하면 다음과 같이 'Create Test for StatefulService'가 뜬다.

<img width="750" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/9d3af253-a3da-4e27-b15d-585096a8a840">

이것을 클릭하고, OK를 누른다. 그러면 편리하게 만들어진다.

<br>

이제 만들어진 <b>StatefulServiceTest클래스</b>의 코드는 다음과 같다.

```java
package hello.core.singleton;

import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;

import static org.assertj.core.api.Assertions.assertThat;

class StatefulServiceTest {

    @Test
    void statefulServiceSingleton() {
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
        StatefulService statefulService1 = ac.getBean(StatefulService.class);
        StatefulService statefulService2 = ac.getBean(StatefulService.class);

        // ThreadA: A사용자 10000원 주문
        statefulService1.order("userA", 10000);
        // ThreadB: B사용자 20000원 주문
        statefulService2.order("userB", 20000);

        // ThreadA: 사용자A 주문 금액 조회
        int price = statefulService1.getPrice();
        System.out.println("price = " + price);

        assertThat(statefulService1.getPrice()).isEqualTo(20000);
    }

    static class TestConfig {

        @Bean
        public StatefulService statefulService() {
            return new StatefulService();
        }
    }
}
```

statefulServiceSingleton이라는 테스트를 생성한다. 그리고 이 StatefulServiceTest 전용으로 config를 하나 만들어주기 위해 아래에 TestConfig를 생성한다. 그리고 ```new AnnotationConfigApplicationContext(TestConfig.class);```를 입력하고 ```cmd + opt + V```를 클릭해 ac를 생성한다. 그리고 이 ac에서 빈을 조회하는 것을 두 개 만든다. 그리고 ```statefulService1.order("userA", 10000);```를 먼저 입력한 다음, 다음 줄에 ```statefulService1.order("userB", 20000);```를 입력한다. A 사용자가 10000원을 주문하고, 그 이후에 B 사용자가 20000원을 주문한 것이다. 이제 여기서 사용자 A가 주문 금액을 조회한다.

사용자 A는 10000원을 주기로 했다. 그러고 나서 사용자가 자기가 주문한 금액을 ```getPrice```로 꺼내고 싶은 것이다. 그런데 그 사이에 사용자 B가 끼어든 것이다. 즉, A가 주문하고 금액을 조회하는 사이에 B가 끼어들어서 20000원으로 주문을 한 것이다. 

그러고 나서 사용자 A가 주문 금액을 조회하면 얼마가 나올까? 테스트를 돌려보면 다음과 같이 나온다.

<img width="1687" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/77bc4c5f-28a3-4867-9d5b-3dcc32961a2e">

20000원이 나온다. 우리가 기대한 것은 10000원이 나오는 것이었다. 왜냐하면 사용자 A가 조회한 것이기 때문이다. (statefulService1을 사용한 것이다.) 그런데도 10000원이 아니라 20000원이 나왔다. 왜냐하면 중간에 사용자 B가 이것을 바꿔버렸기 때문이다.

statefulService1이든 statefulService2이든, 상관없다. 인스턴스는 같은 것을 사용한다.

그리고 검증을 위해 아래에 ```assertThat(statefulService1.getPrice()).isEqualTo(20000);```을 입력한다. 이제 실행하면 테스트는 성공한다. 서비스 측면에서는 완전히 망한 것이다. 왜냐하면 사용자 A는 10000원을 주문 넣었는데, 주문서를 확인해보니 주문 결과 화면에 20000원으로 보이는 것이다. 난리 난다. 이렇게 보이는 것만 바뀌면 그나마 다행이지만, 만약 A가 10000원을 주문 넣었는데 중간에 B가 끼어들어서 20000원 주문을 넣었고, 실제 카드에 긁힌 금액이 A 사용자가 10000원이 아니라 20000원이면 매우 심각한 상황이다. 그러면 이 서비스는 이제 망하는 것이고, 몇 백억 물어주고 접어야 하는 것이다.

<br>

다시 위 StatefulServiceTest 코드를 보자. ```statefulService1.order("userA", 10000);```를 해서 사용자 A는 10000원을 주문한다. 그러면 다음과 같이 StatefulService 클래스의 ```this.price```의 price에 10000원이 들어가서 ```private int price```의 price에 10000원이 할당된다.

<img width="629" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/47635aad-856d-438b-8a41-3aa0fb1d1b88">

그런데 사용자 B가 또 들어와서 ```statefulService2.order("userB", 20000);```를 통해 20000원을 넣으면, 다시 StatefulService 클래스의 ```private int price```의 price가 20000원으로 세팅된다. 원래 10000원으로 되어 있던 것이 20000원으로 바꿔치기 된 것이다. statefulService1과 statefulService2가 똑같은 것이기 때문이다.

그래서 사용자 A가 주문서 넣고 주문 결과 화면 확인했는데 ```getPrice```로 꺼내니 20000원을 꺼내게 된 것이다.

<br>

정리하면 다음과 같다.

* 최대한 단순히 설명하기 위해, 실제 쓰레드는 사용하지 않았다.
* ThreadA가 사용자 A의 코드를 호출하고 ThreadB가 사용자 B 코드를 호출한다 가정하자.
* StatefulService의 price 필드는 공유되는 필드인데, 특정 클라이언트가 값을 변경한다.
* 사용자 A의 주문금액은 10000원이 되어야 하는데, 20000원이라는 결과가 나왔다.
* 실무에서 이런 경우를 종종 보는데, 이로 인해 정말 해결하기 어려운 큰 문제들이 터진다. (몇 년에 한 번씩 꼭 만난다고 한다.)
* 진짜 공유 필드는 조심해야 한다! 스프링 빈은 항상 무상태(stateless)로 설계해야 한다.

<br>

StatefulService클래스의 코드를 다음과 같이 수정한다.

<img width="585" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4a81fb6b-358d-4a35-813a-f7f3944b2481">

그리고 StatefulServiceTest클래스의 코드를 다음과 같이 수정한다.

<img width="921" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/338184ae-84a6-4f5a-901f-2a7f907d6b84">

이제 테스트를 돌려보면 다음과 같이 나온다.

<img width="1684" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f167fedd-6f83-4904-ab7d-572189e31be7">

사용자 A의 금액이 10000원으로 정상적으로 나온다. 지역변수로 공유되는 것이 아니기 때문이다. 이런 식으로 문제를 해결해야 한다.

<br>

실무에서는 매우 복잡한 상황에서 문제가 터지기 때문에 원인을 찾기 어렵다.


## 5. @Configuration과 싱글톤

이번에는 ```@Configuration```의 비밀에 대해 파헤쳐 보겠다. ```@Configuration```은 사실 싱글톤을 위해 존재하는 것이다.

기존의 AppConfig클래스의 코드를 보면 다음과 같다.

```java
package hello.core;

import hello.core.discount.DiscountPolicy;
import hello.core.discount.RateDiscountPolicy;
import hello.core.member.MemberRepository;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    // @Bean memberService → new MemoryMemberRepository()
    // @Bean orderService → new MemoryMemberRepository()

    @Bean
    public MemberService memberService() {

        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public OrderService orderService() {

        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    @Bean
    public MemberRepository memberRepository() {

        return new MemoryMemberRepository();
    }

    @Bean
    public DiscountPolicy discountPolicy() {
//        return new FixDiscountPolicy();
        return new RateDiscountPolicy();
    }
}
```

위 코드에서 이상한 것이 하나 있다. 만약 스프링 빈이 memberService를 생성하고 호출할 때, new MemberServiceImpl을 호출하면서 memberRepository를 호출한다. 자바 코드이니 이 메서드가 호출될 것이다. 그러면 ```new MemoryMemberRepository```가 생성된다.

```@Bean```이 되어 있는데, memberService()를 하면 memberRepository()를 호출하면서 결과적으로 ```new MemoryMemberRepository()```를 한 번 호출해주는 것이다.

그 다음에 orderService 메서드를 호출할 때, ```new OrderServiceImpl```을 생성하는 것은 당연하고, memberRepository() 메서드를 또 호출한다. 그러면 이게 들어가서 바로 위에 ```new MemoryMemberRepository()```가 호출되는 것이다.

지금 설명한 것들만 두 번 호출되었다. 그러면 싱글톤이 깨지는 것이 아닐까 하는 의문이 들게 된다.

<br>

위에서 한 것을 정리하자면 다음과 같다.

* memberService 빈을 만드는 코드를 보면 ```memberRepository()```를 호출한다.
    * 이 메서드를 호출하면 ```new MemoryMemberRepository()```를 호출한다.
* orderService 빈을 만드는 코드도 동일하게 ```memberRepository()```를 호출한다.
    * 이 메서드를 호출하면 ```new MemoryMemberRepository()```를 호출한다.

결과적으로 각각 다른 2개의 ```MemoryMemberRepository```가 생성되면서 싱글톤이 깨지는 것처럼 보인다. 스프링 컨테이너는 이 문제를 어떻게 해결할까?

<br>

개발을 하다가 이렇게 고민이 되면, 테스트 코드를 돌려보면 된다.

테스트 하는 것은 간단하다. MemberServiceImpl에 있는 MemberRepository의 값을 확인해보면 된다. (OrderServiceImpl 또한 마찬가지)

<b>MemberServiceImpl클래스</b>에 다음과 같이 코드를 추가한다.

```java
package hello.core.member;

public class  MemberServiceImpl implements MemberService {

    private final MemberRepository memberRepository;

    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    @Override
    public void join(Member member) {
        memberRepository.save(member);
    }

    @Override
    public Member findMember(Long memberId) {
        return memberRepository.findById(memberId);
    }

    // 테스트 용도
    public MemberRepository getMemberRepository() {
        return memberRepository;
    }
}
```

그리고 <b>OrderServiceImpl클래스</b>에도 다음과 같이 코드를 추가한다.

```java
package hello.core.order;

import hello.core.discount.DiscountPolicy;
import hello.core.member.Member;
import hello.core.member.MemberRepository;

public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        int discountPrice = discountPolicy.discount(member, itemPrice);

        return new Order(memberId, itemName, itemPrice, discountPrice);
    }

    // 테스트 용도
    public MemberRepository getMemberRepository() {
        return memberRepository;
    }
}
```

<br>

이제 테스트를 만들어 보겠다. singleton패키지 안에 ConfigurationSingletonTest클래스를 생성한다.

<b>ConfigurationSingletonTest클래스</b>의 코드는 다음과 같다.

```java
package hello.core.singleton;

import hello.core.AppConfig;
import hello.core.member.MemberRepository;
import hello.core.member.MemberServiceImpl;
import hello.core.order.OrderServiceImpl;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import static org.assertj.core.api.Assertions.*;

public class ConfigurationSingletonTest {

    @Test
    void configurationTest() {
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

        MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);
        OrderServiceImpl orderService = ac.getBean("orderService", OrderServiceImpl.class);
        MemberRepository memberRepository = ac.getBean("memberRepository", MemberRepository.class);

        MemberRepository memberRepository1 = memberService.getMemberRepository();
        MemberRepository memberRepository2 = orderService.getMemberRepository();

        System.out.println("memberService → memberRepository = " + memberRepository1);
        System.out.println("orderService → memberRepository = " + memberRepository2);
        System.out.println("memberRepository = " + memberRepository);

        assertThat(memberService.getMemberRepository()).isSameAs(memberRepository);
        assertThat(orderService.getMemberRepository()).isSameAs(memberRepository);
    }
}
```

ac를 만들고, AppConfig.class를 사용한다. 이제 꺼내보겠다. getBean 해서 "memberService"를 꺼내고, MemberServiceImpl로 보낸다. 그리고 "orderService"를 하고 OrderServiceImpl로 꺼낸다. Impl로 꺼내야 이전에 테스트 용도로 만든 ```getMemberRepository()```를 사용할 수 있기 때문이다. (원래는 이렇게 구체 타입으로 꺼내는 것이 좋지 않다.)

그리고 memberService에서 getMemberRepository로 꺼내 memberRepository1, orderService에서 getMemberRepository로 꺼내 memberRepository2를 생성한다.

이제 출력을 해보겠다. 1번은 "memberService → memberRepository"로 조회하는 것이고, 2번은 "orderService → memberRepository"로 조회하는 것이다. 같은 것인지 다른 것인지 보는 것이다. 검증을 하기 전에 한 가지 더 보겠다. 진짜 memberRepository까지 조회해보는 것이다. ```ac.getBean("memberRepository", MemberRepository.class);```를 통해 한다.

이제 출력하면 다음과 같다.

<img width="1681" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/85713aa3-addb-4e98-b6c7-138062e28fe5">

세 개의 스프링 빈이 다 똑같다.

<br>

분명히 스프링 빈이 memberService()를 호출하면, memberRepository()가 호출된다. 그러면 ```new MemoryMemberRepository()```로 호출한다. new가 두 번 호출되는 것이다. orderService()를 호출하면, OrderServiceImpl 안에 있는 memberRepository()가 호출된다. 그러면 또 ```new MemoryMemberRepository()```가 호출된다. 3번의 new가 호출되는 것이 맞다. 그런데 실행 결과를 보면 같은 인스턴스가 조회된다.

<br>

정리하자면 다음과 같다.

* 확인해보면 memberRepository 인스턴스는 모두 같은 인스턴스가 공유되어 사용된다.
* AppConfig의 자바 코드를 보면 분명히 각각 2번 ```new MemoryMemberRepository``` 호출해서 다른 인스턴스가 생성되어야 하는데? (위의 경우 세 번)
* 어떻게 된 일일까? 혹시 두 번 호출이 안 되는 것일까? 실험을 통해 확인해보자.

<br>

AppConfig에서 그냥 출력을 해보겠다. AppConfig클래스에 다음과 같이 로그를 출력하는 문구들을 추가한다.

```java
package hello.core;

import hello.core.discount.DiscountPolicy;
import hello.core.discount.RateDiscountPolicy;
import hello.core.member.MemberRepository;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public MemberService memberService() {
        System.out.println("call AppConfig.memberService");
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public OrderService orderService() {
        System.out.println("call AppConfig.orderService");
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    @Bean
    public MemberRepository memberRepository() {
        System.out.println("call AppConfig.memberRepository");
        return new MemoryMemberRepository();
    }

    @Bean
    public DiscountPolicy discountPolicy() {
//        return new FixDiscountPolicy();
        return new RateDiscountPolicy();
    }
}
```

결과가 어떻게 출력될까? 먼저 memberService()의 "call AppConfig.memberService"이 출력된다. ```@Bean```이 되어 있는 memberService가 컨테이너에 들어간다. 그러면 AppConfig.memberService를 호출한다. 그 다음에 ```new MemberServiceImpl()``` 하면서 memberRepository()를 호출한다. memberRepository()가 호출되면 "call AppConfig.memberRepository"가 출력된다. 그 다음 orderService의 경우 "call AppConfig.orderService"가 출력되고, 그 다음에 얘가 memberRepository()를 호출하니까 메서드 안에서 memberRepository()가 호출된다. 그러면 "call AppConfig.memberRepository"가 또 출력된다.

출력되는 것의 순서는 다음과 같다. (약간 차이가 있을 수는 있다.)

```java
// call AppConfig.memberService
// call AppConfig.memberRepository
// call AppConfig.memberRepository
// call AppConfig.orderService
// call AppConfig.memberRepository
```

최종적으로, 순서는 좀 다를 수 있지만 memberRepository 메서드가 3번 호출되어야 한다.

<br>

이를 테스트 해보기 위해, ConfigurationSingletonTest클래스의 <b>configurationTest()</b>를 실행시켜보겠다. 실행 결과는 다음과 같다.

<img width="1685" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/de0883b2-9b6d-486d-adfb-5396304a5aa6">

call이 총 3개 있다. 우리의 의도와 완전히 다르게 다음과 같이 세 번 호출되었다.

```java
// call AppConfig.memberService
// call AppConfig.memberRepository
// call AppConfig.orderService
```

"call AppConfig.memberRepository"가 세 번 호출되어야 되는데, 메서드에서 한 번만 호출된 것이다.

이것을 통해 스프링이 어떠한 방법을 써서라도 싱글톤을 보장해준다는 것을 알 수 있다.