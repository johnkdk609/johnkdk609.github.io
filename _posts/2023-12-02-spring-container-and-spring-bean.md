---
layout: post
title: 스프링 컨테이너와 스프링 빈
description: 스프링 핵심 원리 - 기본편
categories: Spring-SpringBoot
date: 2023-12-02 17:02:00 +0900
---
스프링 컨테이너와 스프링 빈에 대해 알아보겠다.

기존에는 스프링의 핵심 원리들, 그리고 객체지향에 대해 공부해봤다. 그렇게 해서 왜 스프링이 만들어졌는지 알아봤다면, 이제부터는 스프링 그 자체에 대해 알아볼 것이다.

<br>

## 목차

1. 스프링 컨테이너 생성
2. 컨테이너에 등록된 모든 빈 조회
3. 스프링 빈 조회 - 기본
4. 스프링 빈 조회 - 동일한 타입이 둘 이상
5. 스프링 빈 조회 - 상속관계
6. BeanFactory와 ApplicationContext
7. 다양한 설정 형식 지원 - 자바 코드, XML
8. 스프링 빈 설정 메타 정보 - BeanDefinition

<br>

## 1. 스프링 컨테이너 생성

스프링 컨테이너가 생성되는 과정을 알아보자.

```java
// 스프링 컨테이너 생성
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```

스프링 컨테이너는 ```new ~~```로 객체를 생성하면서, AppConfig.class를 파라미터로 넘긴다. 이렇게 하면 반환값으로 applicationContext가 반환되었다.

ApplicationContext를 스프링 컨테이너라고 한다. ApplicationContext는 인터페이스이다. 다형성이 적용되어 있다. ApplicationContext 인터페이스를 구현한 것 중 하나가 AnnotationConfigApplicationContext인 것이다.

<img width="225" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5004e983-26e4-4d37-bdc1-ff26b5980db4">

위와 같이 AppConfig는 java Config로 만들어져 있다. 그래서 이름 그대로 Annotation 기반의 java config 설정을 기반으로 스프링을 만들어야 하는 것이다. (Xml~~ApplicationContext와 같이 XML 기반으로 만드는 것도 있다.)

스프링 컨테이너는 XML 기반으로 만들 수 있고, 어노테이션 기반의 자바 설정 클래스를 만들 수도 있다. 요즘에는 XML 기반으로 만드는 것은 잘 사용하지 않는다. 스프링 부트 자체가 어노테이션 기반으로 편리하게 작동되도록 되어있기 때문이다. 요즘에는 거의 어노테이션 방식을 사용한다.

직전에 AppConfig를 사용했던 방식이 어노테이션 기반의 자바 설정 클래스로 스프링 컨테이너를 만든 것이다.

자바 설정 클래스를 기반으로 스프링 컨테이너 ```ApplicationContext```를 만들어보자.

> ```new AnnotationConfigApplicationContext(AppConfig.class);```\
> 이 클래스는 ApplicationContext 인터페이스의 구현체이다.

(참고: 더 정확히는 스프링 컨테이너를 부를 때 BeanFactory, ApplicationContext로 구분해서 이야기한다. BeanFactory를 직접 사용하는 경우는 거의 없으므로 일반적으로 ApplicationContext를 스프링 컨테이너라고 한다.)


### 스프링 컨테이너 생성 과정

생성 과정을 그림으로 보면 다음과 같다.

<b>1. 스프링 컨테이너 생성</b>

<img width="781" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/50bd40bc-5b81-413e-ab69-e6b1fbffa753">

먼저 ```new AnnotationConfigApplicationContext(AppConfig.class)```를 통해 AppConfig에 정보를 준다. 그러면 스프링 컨테이너가 만들어진다. 이 스프링 컨테이너 안에는 스프링 빈 저장소라는 것이 있다. 그래서 key는 '빈 이름'이 되고, value는 '빈 객체'가 되는 것이다.

스프링 컨테이너를 생성할 때에는 구성 정보(AppConfig)를 지정해줘야 한다. 이게 바로 ```AppConfig.class```를 구성 정보(파라미터)로 넣어주는 것이다.

<br>

<b>2. 스프링 빈 등록</b>

<img width="787" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b79192de-af5b-4bd0-aa45-4ec9b0ba94df">

그러면 그 다음 단계는 스프링 컨테이너가 스프링 빈 저장소에 스프링 빈을 등록한다. 파라미터로 넘어온 설정 클래스 정보를 사용해서 스프링 빈을 등록하는 것이다.

```@Bean```이 붙은 것을 보고, 붙은 것들을 전부 호출한다. 호출해보니 memberService라는 메서드 name이 있다. 이 메서드 네임을 key(빈 이름)로 지정하고, 이것에 대한 값으로 ```new MemberServiceImpl~~``` 객체를 빈 객체로 등록한다. 현재 AppConfig에는 네 개가 있으니 네 개를 전부 스프링 빈에 등록한다.

이것을 스프링 빈이라고 한다. 빈 이름은 보통 메서드 이름을 사용한다. 하지만 임의로 부여할 수 있다. ```@Bean(name="memberService2")```의 방식.

<b>주의: 빈 이름은 항상 다른 이름을 부여해야 한다.</b> 같은 이름을 부여하면, 다른 빈이 무시되거나, 기존 빈을 덮어버리거나 설정에 따라 오류가 발생한다. 실무에서는 무조건 단순하고 명확하게 개발해야 한다.

<br>

<b>3. 스프링 빈 의존관계 설정 - 준비</b>

<img width="788" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/afead662-dc45-4337-94b9-5c1432094f70">

그 다음에 스프링이 어떤 단계를 거치냐면, 스프링 빈 의존관계 설정을 준비한다. 위에서 객체를 생성했다. 이번에는 의존관계를 넣어주는 것이다.

<br>

<b>4. 스프링 빈 의존관계 설정 - 완료</b>

<img width="789" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7169073f-6888-4382-af85-584781a67b1d">

위와 같이 memberService의 의존관계로 memberRepository를 넣어준다. 그러면 memoryMemberRepository가 세팅이 된다. orderService는 memberRepository도 의존하고 discountPolicy도 의존한다. 그래서 discountPolicy 스프링 빈, 실제로는 RateDiscountPolicy가 걸린다. 이렇게 해서 동적인 객체 인스턴스 의존관계를 스프링 빈이 연결해주는 것이다. 객체의 참조값들이 다 연결되는 것이다.

스프링 컨테이너는 설정 정보를 참고해서 의존관계를 주입(DI)한다. 설정 정보를 참조한다는 것은, 위 그림에서처럼 "나는 memberRepository 의존할 거야" 와 같은 것들을 보고 의존관계를 주입하는 것이다.

단순히 자바 코드를 호출하는 것 같지만, 차이가 있다. (이 차이는 뒤에서 싱글톤 컨테이너에서 설명될 것이다.)

<br>

참고: 스프링 빈을 생성하고, 의존관계를 주입하는 단계가 나누어져 있다. 그런데 이렇게 자바 코드로 스프링 빈을 등록하면 생성자를 호출하면서 의존관계 주입도 한 번에 처리된다. 왜냐하면 memberService를 생성하면 스프링이 "memberService를 생성해" 하면서 호출한다. ```@Bean```이 memberService에 붙어 있으니, ```new memberServiceImpl(~~)``` 호출하면서 여기서 memberRepository 코드를 호출한다. 그러면 아래에서 MemoryMemberRepository를 호출하는 것이다. 얘를 부르는 순간 의존관계가 다 엮여버리는 것이다. 그런데 사실은 이것뿐만 아니라, 의존관계가 자동으로 연결이 되는 경우도 있다. 그래서 실제 단계는 두 단계로 나뉜다. (자세한 내용은 의존관계 자동 주입에서 다시 알아보겠다.)

### 정리

스프링 컨테이너를 생성하고, 설정(구성) 정보를 참고해서 스프링 빈도 등록하고, 의존관계도 설정했다. 이제 스프링 컨테이너에서 데이터를 조회해보자.


## 2. 컨테이너에 등록된 모든 빈 조회

이번에는 컨테이너에 등록한 빈들이 제대로 등록되었는지 확인해보겠다. 무언가 등록을 한 것 같기는 한데, ```@Bean```을 붙이면 자동으로 등록이 되어 버리니, 진짜 잘 등록되었는지 보고 싶은 것이다.

우선 테스트 코드로 한 번 짜보겠다.

hello.core에 'beanfind'라는 패키지를 만들고, 그 안에 'ApplicationContextInfoTest'라는 클래스를 생성한다.

<b>ApplicationContextInfoTest클래스</b>의 코드는 다음과 같다.

```java
package hello.core.beanfind;

import hello.core.AppConfig;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class ApplicationContextInfoTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
    @DisplayName("모든 빈 출력하기")
    void findAllBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            Object bean = ac.getBean(beanDefinitionName);
            System.out.println("name = " + beanDefinitionName + " object = " + bean);
        }
    }
}
```

우선 AnnotationConfigApplicationContext ac를 생성한다. 그리고 등록된 모든 스프링 빈을 출력해보겠다. JUnit5부터는 public 설정 안 해도 된다. 그래서 그냥 void로 시작한다.

```ac.getBeanDefinitionNames();```로 스프링 빈의 이름을 꺼내고, beanDefinitionNames 리스트에 담는다. 그리고 ```iter```을 입력하고, 엔터를 누르면 for문이 자동으로 완성된다. 그리고 ```ac.getBean(beanDefinitionName);```를 입력하여 빈을 꺼낸다. 그러면 타입을 모르기 때문에 object가 꺼내진다. (타입을 지정하지 않았기 때문)

그 다음에 ```soutv```를 입력하고 엔터를 누른 다음, name은 beanDefinitionName을, object는 bean을 입력한다.

<br>

이제 실행시키면 다음과 같이 나타난다.

<img width="1685" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/aa6f9a23-56e8-46d5-8e5e-cca13735f086">

위 결과를 보면, 스프링 컨테이너에 있는 모든 빈이 가져와진 것을 볼 수 있다.

<img width="1308" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/273af028-7b20-44e1-b1a2-0acb1e1061bf">

위 그림에서 볼 수 있는 이것들은, 스프링 내부적으로 자체를 확장하기 위해서 쓰는 기반 bean들이다. 참고로 appConfig도 스프링 빈으로 등록된다.

<img width="788" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5b12d1f0-f7b4-4185-b97c-f50e119f9faa">

그리고 이 네 개가 내가 등록한 것들이다.

<br>

그런데 스프링 내부에 있는 것들은 좀 빼고 내가 짠 코드만 보고 싶을 수 있다. 그러면 ApplicationBean이라는 것만 출력해볼 것이다.

<br>

ApplicationContextInfoTest에서 작성한 ```@Test``` 코드를 전체 선택하고, ```cmd + D```를 눌러 그대로 아래에 복사한다.

추가한 코드는 다음과 같다.

```java
@Test
@DisplayName("애플리케이션 빈 출력하기")
void findApplicationBean() {
    String[] beanDefinitionNames = ac.getBeanDefinitionNames();
    for (String beanDefinitionName : beanDefinitionNames) {
        BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

        // Role ROLE_APPLICATION: 직접 등록한 애플리케이션 빈
        // Role ROLE_INFRASTRUCTURE: 스프링이 내부에서 사용하는 빈
        if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
            Object bean = ac.getBean(beanDefinitionName);
            System.out.println("name = " + beanDefinitionName + " object = " + bean);
        }
    }
}
```

이번에는 Application Bean을 출력하는 것이니 ```@DisplayName("애플리케이션 빈 출력하기")```로 설정해둔다. 그리고 ```ac.getBeanDefinitionNames();```을 입력해 빈 하나하나의 메타데이터 정보를 꺼낸다.

그리고 조건문으로 ```beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION```을 넣어 스프링이 내부에서 무언가 하기 위해 등록한 빈들이 아니라, 내가 애플리케이션을 개발하기 위해 등록한 빈들의 경우에만 출력하도록 한다.

이제 실행하면 다음과 같이 나온다.

<img width="1684" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/56a9275e-ac25-4770-b595-62195cd1c91f">

appConfig 포함해서 총 다섯 개가 출력된다. memberService의 경우 MemberServiceImpl, memberRepository는 MemoryMemberRepository, orderService는 OrderServiceImpl, discountPolicy는 RateDiscountPolicy에 대한 클래스 인스턴스들이 생성되었다.

<br>

만약에 위 코드에서 ```BeanDefinition.ROLE_INFRASTRUCTURE```로 수정하고 테스트를 다시 실행하면 다음과 같이 출력된다.

<img width="1681" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/da936b4a-ba2b-4edc-8b51-e26322a6cd98">

ROLE_INFRASTRUCTURE은 스프링 컨테이너 내부에서 사용하는 빈들이다. internal~~라고 써 있는 것을 보면 알 수 있다.

<br>

정리하면 다음과 같다.

<b>모든 빈 출력하기</b>

* 실행하면 스프링에 등록된 모든 빈 정보를 출력할 수 있다.
* ```ac.getBeanDefinitionNames()```: 스프링에 등록된 모든 빈 이름을 조회한다.
* ```ac.getBean()```: 빈 이름으로 빈 객체(인스턴스)를 조회한다.

<b>애플리케이션 빈 출력하기</b>

* 스프링이 내부에서 사용하는 빈은 제외하고, 내가 등록한 빈만 출력해볼 수 있다.
* 스프링이 내부에서 사용하는 빈은 ```getRole()```로 구분할 수 있다.
    * ```ROLE_APPLICATION```: 일반적으로 사용자가 정의한 빈
    * ```ROLE_INFRASTRUCTURE```: 스프링이 내부에서 사용하는 빈


## 3. 스프링 빈 조회 - 기본

이번에는 스프링 빈을 조회하는 가장 기본적인 방법부터 하나씩 알아볼 것이다.

빈을 조회하는 가장 간단한 방법은 getBean이라는 메서드를 사용하는 것이다.

```ac.getBean(빈이름, 타입)```

그리고, 빈 이름을 생략하고 타입만 주어도 된다.

```ac.getBean(타입)```

만약 조회한 스프링 빈이 없으면 예외가 발생한다.

* ```NoSuchBeanDefinitionException No bean named 'xxxxx' available```

<br>

우선 테스트의 beanfind 패키지에 ApplicationContextBasicFindTest 클래스를 생성한다. <b>ApplicationContextBasicFindTest클래스</b>의 코드는 다음과 같다.

```java
package hello.core.beanfind;

import hello.core.AppConfig;
import hello.core.member.MemberService;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

class ApplicationContextBasicFindTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
    @DisplayName("빈 이름으로 조회")
    void findBeanByName() {
        MemberService memberService = ac.getBean("memberService", MemberService.class);
        System.out.println("memberService = " + memberService);
        System.out.println("memberService.getClass() = " + memberService.getClass());
    }
}
```

일단 검증을 위해 위와 같이 했다. 실행을 해보면 다음과 같다.

<img width="1684" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f5840b9b-5394-4c67-a6a1-6e47ea6da83f">

기대했던 대로 잘 나왔다. MemberServiceImpl이 나왔고, getClass 타입도 MemberServiceImpl로 잘 나왔다.

<br>

검증은 Assertions(org.assertj.core.api)로 해야 한다. 수정한 코드는 다음과 같다.

```java
@Test
@DisplayName("빈 이름으로 조회")
void findBeanByName() {
    MemberService memberService = ac.getBean("memberService", MemberService.class);
    Assertions.assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
}
```

이제 실행해보면 다음과 같이 성공적으로 돌아간다.

<img width="1684" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/d71b781a-60ba-4c3e-b306-c7417a8a9941">

memberService가 MemberServiceImpl의 인스턴스이면 성공한 것이다. 이제 Assertions 부분을 클릭하고 ```opt + Enter```을 누른 다음, 'Add on-demand ~'을 클릭한다.

<img width="780" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/6810a58f-28de-4370-97c7-d2d96ceaeab7">

<br>

두 번째는 이름 없이 타입으로만 조회해볼 것이다. 추가한 코드는 다음과 같다.

```java
@Test
@DisplayName("이름 없이 타입으로만 조회")
void findBeanByType() {
    MemberService memberService = ac.getBean(MemberService.class);
    assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
}
```

위에서 ```ac.getBean("memberService", MemberService.class);```에서 이름 부분인 "memberService"만 빼면 된다. 그러면 타입으로만 조회하는 것이다. 훨씬 편리하기는 한데, 장단점이 있다. 타입으로만 할 경우 같은 타입들이 여러 개일 경우 곤란해진다.

실행시키면 테스트는 성공한다.

<br>

지금까지는 인터페이스로 조회했다. 이렇게 인터페이스로 조회하면 인터페이스의 구현체가 대상이 된다. 그런데 구체 타입으로 조회할 수도 있다.

추가한 코드는 다음과 같다.

```java
@Test
@DisplayName("구체 타입으로 조회")
void findBeanByName2() {
    MemberService memberService = ac.getBean("memberService", MemberServiceImpl.class);
    assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
}
```

이번에는 ```ac.getBean("memberService", MemberServiceImpl.class);```을 입력하는 것이다. 이번에는 인터페이스명 MemberService가 아니라 MemberServiceImpl을 입력한 것이다.

실행하면 테스트는 성공한다.

AppConfig클래스를 보면 다음과 같다.

<img width="793" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/27bbf275-6310-4fa5-999a-778d7a96ac0e">

반환 타입을 가령 MemberService로 했는데, 이 타입이어야 할 필요는 없다. 스프링 빈에 등록된 인스턴스 타입을 보고 결정하기 때문에, 꼭 여기에 있는 인터페이스가 아니어도 된다. 실제 구체적인 것으로 적어줘도 된다. 

물론 구체적인 것을 적는 것은 좋지 않다. 항상 역할과 구현을 구분해야 된다. 그리고 역할에 의존해야 한다. 위의 경우 구현에 의존하는 것이므로 그렇게 좋은 코드는 아니다. (살다보면 모든 것이 이상적으로 돌아가지 않을 때가 있다. 그럴 때 이렇게 하면 된다.)

<br>

만약 조회가 안 되면, 실패 테스트를 만들면 된다.

추가한 코드를 포함한 ApplicationContextBasicFindTest의 코드는 다음과 같다.

```java
package hello.core.beanfind;

import hello.core.AppConfig;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoSuchBeanDefinitionException;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import static org.assertj.core.api.Assertions.*;
import static org.junit.jupiter.api.Assertions.*;

class ApplicationContextBasicFindTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
    @DisplayName("빈 이름으로 조회")
    void findBeanByName() {
        MemberService memberService = ac.getBean("memberService", MemberService.class);
        assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
    }

    @Test
    @DisplayName("이름 없이 타입으로만 조회")
    void findBeanByType() {
        MemberService memberService = ac.getBean(MemberService.class);
        assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
    }

    @Test
    @DisplayName("구체 타입으로 조회")
    void findBeanByName2() {
        MemberService memberService = ac.getBean("memberService", MemberServiceImpl.class);
        assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
    }

    @Test
    @DisplayName("빈 이름으로 조회")
    void findBeanByNameX() {
        // ac.getBean("xxxxx", MemberService.class);
        assertThrows(NoSuchBeanDefinitionException.class,
                () -> ac.getBean("xxxxx", MemberService.class));
    }
}
```

테스트는 항상 실패 테스트를 만들어야 한다. findBeanByNameX라는 이름으로 생성한다. 처음에 ```MemberService xxxxx = ac.getBean("xxxxx", MemberService.class);```을 입력한 다음 테스트를 실행하면, 다음과 같이 나온다.

<img width="1684" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/d9121776-4ec7-44c8-8c5e-6e54e0910001">

예외가 발생한다. "NoSuchBeanDefinitionException: No bean named 'xxxxx' available"로 이런 이름의 빈을 등록한 적이 없다는 것이다.

이것을 테스트 코드를 검증해야 하는데, 자바 8의 람다 기능을 쓸 것이다. ```Assertions```의 ```org.junit.jupiter.api``` 것을 사용한다. 그런데 너무 기니까 ```opt + Enter```을 누르고 static import를 한다. 그리고 ```assertThrows(NoSuchBeanDefinitionException.class, () -> ac.getBean("xxxxx", MemberService.class));```을 입력한다. 이 예외가 터질 경우에 성공하는 것이고, 안 터지면 실패이다.

실행시키면 다음과 같이 성공적으로 된다.

<img width="1683" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7b8d27a9-2ea1-4163-9c76-8427e52292cc">

참고로 구체 타입으로 조회하면 유연성이 떨어진다.


## 4. 스프링 빈 조회 - 동일한 타입이 둘 이상

타입으로 조회 시 같은 타입의 스프링 빈이 둘 이상이면 오류가 발생한다. <u>이때는 빈 이름을 지정해야 한다.</u>

```ac.getBeansOfType()```을 사용하면 해당 타입의 모든 빈을 조회할 수 있다.

<br>

테스트 쪽에 beanfind 패키지 안에 ApplicationContextSameBeanFindTest클래스를 생성한다.

<b>ApplicationContextSameBeanFindTest클래스</b>의 코드는 다음과 같다.

```java
package hello.core.beanfind;

import hello.core.AppConfig;
import hello.core.discount.DiscountPolicy;
import hello.core.member.MemberRepository;
import hello.core.member.MemoryMemberRepository;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

public class ApplicationContextSameBeanFindTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SameBeanConfig.class);

    @Test
    @DisplayName("타입으로 조회 시 같은 타입이 둘 이상 있으면, 중복 오류가 발생한다.")
    void findBeanByTypeDuplicate() {
        MemberRepository bean = ac.getBean(MemberRepository.class);
    }

    @Configuration
    static class SameBeanConfig {

        @Bean
        public MemberRepository memberRepository1() {
            return new MemoryMemberRepository();
        }

        @Bean
        public MemberRepository memberRepository2() {
            return new MemoryMemberRepository();
        }
    }
}
```

우선 findBeanByTypeDuplicate를 생성한다. ```ac.getBean(DiscountPolicy.class)```를 입력할 경우, AppConfig를 손을 대야 한다. AppConfig는 손 대기 싫으니까, Config파일을 새로 만들어볼 것이다. 아래에 ```static class SameBeanConfig```를 만든다. static을 쓴 것을 봤을 때, 클래스 안에다가 클래스를 썼다는 것은, 이것은 이 안에서만 쓰겠다는 것이다. 그리고 그 안에 memberRepository1과 memberRepository2를 만든다. 빈의 이름이 다르고, 인스턴스의 타입이 같을 수 있다. 그리고 위에서 ```ac.getBean(MemberRepository.class);```로 수정한 다음, ```cmd + opt + V```를 클릭한다. 그리고 맨 위에 ```new AnnotationConfigApplicationContext(SameBeanConfig.class);```로 수정한다. 이렇게 하면 스프링 컨테이너가 뜰 때, 이 안에 있는 Config 즉, SameBeanConfig만 가지고 실행하는 것이다. 스프링 컨테이너는 이 안에 있는 스프링 빈 두 개만(memberRepository1, memberRepository2) 등록한다. 이렇게 해서 돌려서 memberRepository를 조회하면 두 개가 튀어나온다. 스프링 입장에서는 무엇을 선택해야 할지 예외가 발생하는 것이다.

실행하면 다음과 같다.

<img width="1684" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2c535789-2ea5-4129-af2e-37106ff14e9c">

위 그림을 보면, NoUniqueBeanDefinitionException이 발생했다. 딱 하나만 있어야 하는데 유니크하지 않다는 것이다. 두 개가 찾아졌고, memberRepository1, memberRepository2가 찾아졌다고 나온다.

<br>

이것을 해결하기 전에, 일단 테스트를 완성시켜보겠다. 수정한 코드는 다음과 같다.

```java
@Test
@DisplayName("타입으로 조회 시 같은 타입이 둘 이상 있으면, 중복 오류가 발생한다.")
void findBeanByTypeDuplicate() {
    assertThrows(NoUniqueBeanDefinitionException.class,
            () -> ac.getBean(MemberRepository.class));
```

돌려보면 테스트는 성공한다.

<img width="1685" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ffde70b9-b0b2-4a3b-9a61-7af72fb5b30c">

이 경우 예외가 터지는 것이 성공 로직인 것이다.

<br>

얘를 실제로 성공시키려면 코드를 다음과 같이 추가한다.

```java
@Test
@DisplayName("타입으로 조회 시 같은 타입이 둘 이상 있으면, 빈 이름을 지정하면 된다.")
void findBeanByName() {
    MemberRepository memberRepository = ac.getBean("memberRepository1", MemberRepository.class);
    assertThat(memberRepository).isInstanceOf(MemberRepository.class);
}
```

findBeanByName의 경우, ApplicationContext에서 이전에는 MemberRepository에서 클래스 타입으로 바로 조회했다. 그래서 ```ac.getBean(MemberRepository.class);```를 입력했고 클래스 타입을 바로 조회했다. 그런데 이렇게 하면 예외가 터졌었다. 이를 해결하기 위해 앞에다가 빈 이름을 지정해주는 것이다. "memberRepository1"을 입력한다. 그리고 ```assertThat(memberRepository).isInstanceOf(MemberRepository.class);```을 입력한다. 그냥 MemberRepository의 인스턴스이면 된다. 더 디테일하게 "1번"으로 해도 되지만, 이 정도로 해도 된다.

실행시키면 다음과 같이 성공적으로 수행된다.

<img width="1687" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ae405b2c-10f9-44eb-ac49-86da2100cf36">

<br>

그 다음에, "나는 둘 다 꺼내고 싶어"라고 할 수도 있다. 이럴 경우에 다음과 같은 코드를 추가한다.

```java
@Test
@DisplayName("특정 타입을 모두 조회하기")
void findAllBeanByType() {
    Map<String, MemberRepository> beansOfType = ac.getBeansOfType(MemberRepository.class);
    for (String key : beansOfType.keySet()) {
        System.out.println("key = " + key + " value = " + beansOfType.get(key));
    }
    System.out.println("beansOfType = " + beansOfType);
    assertThat(beansOfType.size()).isEqualTo(2);
}
```

findAllBeanByType를 입력하고, ```ac.getBeansOfType(MemberRepository.class);```를 입력한 다음, ```cmd + opt + V```를 클릭하면 key-value의 Map으로 나온다. 그리고 iter을 돌린다. 이름을 key라고 하고, key를 출력하고 value는 ```beansOfType.get(key)```를 넣는다. 또 beansOfType도 출력해보겠다. 검증의 경우 ```assertThat(beansOfType.size()).isEqualTo(2);```를 입력한다. 해당 타입의 빈이 두 개 등록되어 있으니, 사이즈가 두 개가 나와야 하는 것이다.

실행시키면 다음과 같이 나타난다.

<img width="1686" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/99145e77-3a56-4b66-b61c-60d69a2f17d3">

key, value가 잘 출력된다.

<br>

이렇게 해서, 중복인 경우(같은 타입이 둘 이상 있을 때) 해결하는 방법과 한 번에 조회하는 방법을 알아봤다.


## 5. 스프링 빈 조회 - 상속관계

스프링 빈을 조회할 때 기본 대원칙은, <b>부모 타입으로 조회하면 자식 타입도 함께 조회하는 것이다.</b> 자식 타입은 다 끌려나온다.

그래서 모든 자바 객체의 최고 부모인 ```Object```타입으로 조회하면, 모든 스프링 빈을 조회한다.

예를 들어, 앞서 생성했던 AnnotationConfigApplicationContext의 경우에도 사실 ```extends Object```를 하고 있는 것이다.

<img width="574" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/91f0ee63-02d9-4a29-bf56-15a4f0138584">

자바는 기본적으로 모든 최상위 부모는 다 Object이다. 눈에 안 보이면 저 코드가 자동으로 들어가는 것으로 생각하면 된다.

<br>

<img width="650" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1a0fd3e8-784b-459c-881b-c719bf8fcf43">

위 그림에서, 1번 타입으로 조회하면 1, 2, 3, 4, 5, 6, 7이 다 나온다. 2번으로 조회하면 2, 4, 5번이 나오고, 3번으로 조회하면 3, 6, 7이 나온다. 4, 5, 6, 7은 마지막 단에 있으니 조회할 때 자기 자신만 조회 된다.

<br>

이제 테스트 코드를 짜보겠다. beanfind 패키지에 ApplicationContextExtendsFindTest를 생성한다. <b>ApplicationContextExtendsFindTest클래스</b>의 코드는 다음과 같다.

```java
package hello.core.beanfind;

import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPolicy;
import hello.core.discount.RateDiscountPolicy;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoUniqueBeanDefinitionException;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.Map;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

public class ApplicationContextExtendsFindTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);

    @Test
    @DisplayName("부모 타입으로 조회 시, 자식이 둘 이상 있으면 중복 오류가 발생한다.")
    void findBeanByParentTypeDuplicate() {
        assertThrows(NoUniqueBeanDefinitionException.class,
                () -> ac.getBean(DiscountPolicy.class));
    }

    @Test
    @DisplayName("부모 타입으로 조회 시, 자식이 둘 이상 있으면, 빈 이름을 지정하면 된다.")
    void findBeanByParentTypeBeanName() {
        DiscountPolicy rateDiscountPolicy = ac.getBean("rateDiscountPolicy", DiscountPolicy.class);
        assertThat(rateDiscountPolicy).isInstanceOf(RateDiscountPolicy.class);
    }

    @Test
    @DisplayName("특정 하위 타입으로 조회")
    void findBeanBySubType() {
        RateDiscountPolicy bean = ac.getBean(RateDiscountPolicy.class);
        assertThat(bean).isInstanceOf(RateDiscountPolicy.class);
    }

    @Test
    @DisplayName("부모 타입으로 모두 조회하기")
    void findAllBeanByParentType() {
        Map<String, DiscountPolicy> beansOfType = ac.getBeansOfType(DiscountPolicy.class);
        assertThat(beansOfType.size()).isEqualTo(2);
        for (String key : beansOfType.keySet()) {
            System.out.println("key = " + key + " value = " + beansOfType.get(key));
        }
    }

    @Test
    @DisplayName("부모 타입으로 모두 조회하기 - Object")
    void findAllBeansByObjectType() {
        Map<String, Object> beansOfType = ac.getBeansOfType(Object.class);
        for (String key : beansOfType.keySet()) {
            System.out.println("key = " + key + " value = " + beansOfType.get(key));
        }
    }

    @Configuration
    static class TestConfig {
        @Bean
        public DiscountPolicy rateDiscountPolicy() {
            return new RateDiscountPolicy();
        }

        @Bean
        public DiscountPolicy fixDiscountPolicy() {
            return new FixDiscountPolicy();
        }
    }
}
```

우선 ```AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext();```를 기입한다. 괄호 안에는 지금 막 생성하는 static class를 넣을 것이다. 맨 아래에 TestConfig를 생성한다. 그리고 Bean을 두 개를 등록할 것인데, rateDiscountPolicy와 fixDiscountPolicy이다. 둘 다 DiscountPolicy타입이므로, 이 타입으로 조회하면 자식 타입인 이 두 개가 조회될 것이다. 그리고 위에 괄호에 ```TestConfig.class```를 넣는다.

이제부터 하나씩 테스트를 만들어볼 것이다. 먼저 "부모 타입으로 조회 시 자식이 둘 이상 있으면 중복 오류가 발생"하는 테스트를 생성해보겠다. 만약 ```DiscountPolicy bean = ac.getBean(DiscountPolicy.class);```만 입력하고 테스트를 실행하면, NoUniqueBeanDefinitionException 오류가 발생한다. 그래서 ```assertThrows(NoUniqueBeanDefinitionException.class, () -> ac.getBean(DiscountPolicy.class))```로 수정하면 실행했을 때 성공적으로 돌아간다.

<img width="1686" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e482a569-befa-4b1b-869c-1c376559905d">

<br>

다음으로, "부모 타입으로 조회 시 자식이 둘 이상 있으면 빈 이름을 지정하면 된다"를 테스트해볼 것이다. ```ac.getBean("rateDiscountPolicy", DiscountPolicy.class);```를 입력하고 ```cmd + opt + V```를 클릭한다. 타입은 DiscountPolicy이지만 실제 구현 객체는 rateDiscountPolicy가 나올 것이다. 그리고 ```assertThat(rateDiscountPolicy).isInstanceOf(RateDiscountPolicy.class);```를 입력해 확인한다. 실행하면 성공적으로 실행된다.

<img width="1689" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2f635402-e3b5-4183-a142-8d945bcb49e6">

<br>

이번에는 "특정 하위 타입으로 조회"하는 테스트를 만들 것이다. (물론 안 좋은 방법이다.) ```ac.getBean(RateDiscountPolicy.class);```를 입력하고 ```cmd + opt + V```를 클릭한다. 하나밖에 없으니 타입을 바로 지정하는 것이다. 그리고 ```assertThat(bean).isInstanceOf(RateDiscountPolicy.class);```를 입력한다. 구체적인 타입으로 지정하면, rateDiscountPolicy는 아래 TestConfig에 하나밖에 없으니 딱 나오는 것이다. 실행하면 다음과 같다.

<img width="1686" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/58b3f99b-6073-4b0e-bcad-9522e681f7bb">

<br>

그러면 이제 이 질문이 있을 수 있다. 아래에 TestConfig에서 ```public DiscountPolicy fixDiscountPolicy() {```에서 DiscountPolicy를 RateDiscountPolicy로 지정해도 똑같다. 그런데 DiscountPolicy로 해두는 이유는, 개발하거나 설계할 때 역할과 구현을 쪼개기 위함이다. DiscountPolicy를 보고 역할을 파악하고, rateDiscountPolicy로 구체적인 것을 보는 것이다. 이렇게 해야 의존관계 주입(DI)할 때에도 DiscountPolicy를 보고 용이하게 할 수 있는 것이다.

<br>

이번에는 "부모 타입으로 모두 조회"하는 테스트를 생성해보겠다. ```ac.getBeansOfType(DiscountPolicy.class);```를 입력하고 ```cmd + opt + V```를 클릭한다. 그리고 ```assertThat(beansOfType.size()).isEqualTo(2);```를 입력해서 타입의 사이즈가 2개면 성공하게 한다. 그리고 ```iter```을 입력하여 key와 value를 출력하게 한다. 참고로 출력하는 것은 공부하는 용도로 입력하는 것이지, 실제 테스트 케이스를 짤 때에 이런 출력물을 만들면 안 된다. 왜냐하면 자동 통과, 실패 여부를 시스템이 결정하게 해야 하기 때문이다. 테스트가 통과한다, 안 한다로만 하면 되고, 이런 ```println``` 같은 것은 다 빼는 것이 좋다. (물론, 테스트 자체도 디버깅 해보고 싶을 수 있으니 그때에는 남겨도 괜찮다.)

실행하면 다음과 같이 key와 value가 잘 나온다.

<img width="1683" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/3e92b72a-4b96-43b6-a55c-9c64617162f2">

<br>

이제 마지막으로 Object타입으로 모두 조회하는 것을 만들어보겠다. ```ac.getBeansOfType(Ojbect.class);```를 입력하고 ```cmd + opt + V```를 클릭한다. 그리고 iter을 입력하여 key와 value를 출력하게 한다. 실행하면 다음과 같다.

<img width="1683" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/3ebaa241-588c-48cb-a958-dbf6bee91958">

보면 스프링에 있는 것들까지 다 튀어나온다. 스프링 안에는 여러 가지 빈들이 등록돼 있는데, 그것들까지 다 딸려서 나오는 것이다. 왜냐하면 자바 객체는 모든 것이 Object타입이기 때문이다. 내가 등록했던 두 가지도 나온다.

<br>

이렇게 해서 조회하는 기본적인 방법들을 거의 다 알아봤다. 사실 내가 ApplicationContext에서 getBean을 직접 할 일이 별로 없다. 그게 아니라 뒤에 가면 스프링 컨테이너에서 자동적으로 의존관계를 주입하는 것을 쓰는 방식으로 주로 한다. 하지만 이것이 기본 기능이기도 하고, 가끔 순수한 자바 애플리케이션에서 스프링 컨테이너를 생성해서 써야 할 경우가 있다. 그럴 때 이런 것을 쓰는 것이다.


## 6. BeanFactory와 ApplicationContext

이번에는 beanFactory와 ApplicationContext에 대해서 알아보겠다.

<img width="754" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/de853588-b936-483d-b4cc-20e7552e81a8">

위 그림을 보면, 최상위에 beanFactory인터페이스가 있고, beanFactory를 상속받은 ApplicationContext인터페이스가 있다. 이 ApplicationContext라는 것은 beanFactory에 부가 기능을 더한 것이라고 이해할 수 있다.

그리고 ApplicationContext 밑에 우리가 사용했던 AnnotationConfigApplicationContext와 같은 구현 클래스가 있다.

<br>

### BeanFactory

* 스프링 컨테이너의 최상위 인터페이스이다.
* 스프링 빈을 관리하고 조회하는 역할을 담당한다.
* ```getBean()```과 같은 기능을 제공한다.
* 지금까지 우리가 사용했던 대부분의 기능은 BeanFactory가 제공하는 기능이다.

### ApplicationContext

* BeanFactory 기능을 모두 상속받아서 제공한다.

```cmd + O```를 누르고 'All places'로 설정한 다음 'ApplicationContext'를 검색하면 다음과 같이 나타난다.

<img width="926" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f3c7c51d-f085-466c-a93b-ad16b6673fe3">

여기서, ApplicationContext인터페이스를 클릭하고 들어가보면 다음과 같이 나온다.

<img width="927" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/03177319-0573-48d7-87ef-8b6014feaf4a">

ApplicationContext가 ListableBeanFactory, HierarchicalBeanFactory를 상속 받고 있다. 여기서 가령 ListableBeanFactory를 자세히 보면, 다음과 같이 BeanFactory를 상속 받고 있다.

<img width="535" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c01f3487-c58a-410a-9501-6c5b4527a344">

* 그러면, 빈을 관리하고 검색하는 기능을 BeanFactory가 제공해주는데, 둘의 차이는 뭘까?
* 애플리케이션을 개발할 때는 빈을 관리하고 조회하는 기능은 물론이고, 수많은 부가기능이 필요하다.

### ApplicationContext가 제공하는 부가기능

<img width="986" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f9bd784b-48c9-4960-9603-5ac6bce29921">

인터페이스가 굉장히 많이 분리되어 있다. ApplicationContext인터페이스가 BeanFactory도 받고 있는데, 더해서 MessageSource, EnvironmentCapable, ApplicationEventPublisher, ResourceLoader 등의 여러 가지 인터페이스들도 받고 있다.

<img width="951" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b0f1d175-d840-4e38-83e8-c55f946387f6">

BeanFactory와 관련된 것뿐만 아니라, 환경설정과 관련된 EnvironmentCapable 등을 가지고 있는 것이다. 하나씩 설명하면 다음과 같다.

<br>

* <b>메세지 소스를 활용한 국제화 기능</b>
    * 예를 들어서 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로 출력하는 웹사이트.
* <b>환경변수</b>
    * 로컬, 개발, 운영 등을 구분해서 처리. (실제로 개발할 때에는 크게 세 가지 환경이 있다. 내 PC인 로컬, 테스트 서버에 띄워두고 테스트할 수 있는 개발 환경, 그리고 실제 프로덕션에 나가는 운영 환경. 한 가지가 더 있다면 스테이지 환경이 있기는 하다.)
* <b>애플리케이션 이벤트</b>
    * 이벤트를 발행하고 구독하는 모델을 편리하게 지원
* <b>편리한 리소스 조회</b>
    * 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회

### 정리

* ApplicationContext는 BeanFactory의 기능을 상속받는다.
* ApplicationContext는 빈 관리기능 + 편리한 부가 기능을 제공한다.
* BeanFactory를 직접 사용할 일은 거의 없다. 부가기능이 포함된 ApplicationContext를 사용한다.
* BeanFactory나 ApplicationContext를 스프링 컨테이너라 한다. (그리고 우리는 ApplicationContext만 쓴다고 생각하면 된다.)


## 7. 다양한 설정 형식 지원 - 자바 코드, XML

지금까지 자바 코드로 설정하는 것을 알아봤는데, 이번에는 XML로 설정하는 것을 잠깐 알아보겠다.

스프링은 다양한 설정 형식을 지원한다. (자바 코드, XML 등) 스프링 컨테이너는 다양한 형식의 설정 정보를 받아들일 수 있게 유연하게 설계되어 있다. 자바 코드, XML, Groovy 등등... 심지어 임의의 무언가를 만들 수도 있다.

<img width="986" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/d0110722-7c38-43e2-bbcb-2bd227223bde">

위 그림을 보면, 제일 위에 BeanFactory인터페이스가 있고, 그 밑에 ApplicationContext가 있다. 이 ApplicationContext를 구현한 것 중에 하나가 이전에 사용했던 AnnotationConfigApplicationContext이다. 그리고 또, ApplicationContext를 구현한 것 중에 GenericXmlApplicationContext라는 것이 있다. 이것은 자바 코드가 아니라, XML이라는 문서를 설정 정보로 사용하는 것이다. 그리고 임의로 구현해서 만들 수도 있다.

주로 많이 사용하는 것은 자바 코드 기반의 AnnotationConfig이다. 과거에는 XML을 많이 사용했었다. (그래서 XML이 어떻게 돌아가는지 알고 있는 것이 좋다.)

<br>

### 어노테이션 기반 자바 코드 설정 사용

지금까지 했던 것이다. ```new AnnotationConfigApplicationContext(AppConfig.class)```를 만들면서 설정 정보를 넣어줬다. ```AnnocationConfigApplicationContext```클래스를 사용하면서 자바 코드로 된 설정 정보를 넘기면 된다.

### XML 설정 사용

최근에는 스프링 부트를 많이 사용하면서 XML 기반의 설정은 잘 사용하지 않는다. 하지만 아직 많은 레거시 프로젝트들이 XML로 되어 있고, 또 XML을 사용하면 컴파일 없이 빈 설정 정보를 변경할 수 있는 장점도 있으므로 한 번쯤 배워두는 것도 괜찮다.

```GenericXmlApplicationContext```를 사용하면서 XML 설정 파일을 넘기면 된다.

<br>

먼저 테스트를 만들어 보겠다. test쪽에서 xml 패키지를 만들고, 그 안에 XmlAppContext클래스를 생성한다. <b>XmlAppContext클래스</b>의 코드는 다음과 같다.

```java
package hello.core.xml;

import hello.core.member.MemberService;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

import static org.assertj.core.api.Assertions.*;

public class XmlAppContext {

    @Test
    void xmlAppContext() {
        ApplicationContext ac = new GenericXmlApplicationContext("appConfig.xml");
        MemberService memberService = ac.getBean("memberService", MemberService.class);
        assertThat(memberService).isInstanceOf(MemberService.class);
    }
}
```

GenericXmlApplicationContext를 생성하고 여기에다가 "appConfig.xml"을 넘길 것이다. 그리고 ac에서 빈을 조회할 것이다. 그리고 ```assertThat(memberService).isInstanceOf(MemberService.class);```를 기입한다. Assertions는 static import 한다.

이 테스트는 현재 실행되지 않는다. ```appConfig.xml```이 아직 생성되지 않았기 때문이다.

<br>

이제 appConfig.xml을 만들어보겠다. 테스트이기 때문에 테스트에 넣어도 되지만, 이번에는 테스트가 아닌 곳에 만들 것이다. XML은 자바 코드가 아니므로 resources 안에 생성하면 된다. resources 안에 appConfig.xml 파일을 생성한다.

<b>appConfig.xml</b>의 코드는 다음과 같다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="memberService" class="hello.core.member.MemberServiceImpl" >
        <constructor-arg name="memberRepository" ref="memberRepository" />
    </bean>

    <bean id="memberRepository" class="hello.core.member.MemoryMemberRepository" />

    <bean id="orderService" class="hello.core.order.OrderServiceImpl" >
        <constructor-arg name="memberRepository" ref="memberRepository" />
        <constructor-arg name="discountPolicy" ref="discountPolicy" />
    </bean>

    <bean id="discountPolicy" class="hello.core.discount.RateDiscountPolicy" />
</beans>
```

우선 id는 "memberService"로, 클래스는 패키지명까지 다 적어서 MemberServiceImpl을 적는다. 그리고 생성자를 넘겨줘야 하기 때문에, constructor-arg로 해서 name은 "memberRepository", 레퍼런스는 ref로 "memberRepository"로 넘긴다. 그런데 지금 memberRepository가 없다. 그래서 memberRepository빈을 등록한다. 아래에 bean을 id는 "memberRepository", 클래스는 MemoryMemberRepository로 해서 넘긴다. 이렇게 하면 memberRepository와 관련된 것이 완성된다.

이전에 만들었던 AppConfig.java를 보면, 빈으로 등록하고 memberService, memberRepository 등록하는 것과 완전히 똑같다.

<img width="556" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5bbcfb99-fa0b-4114-b3fb-6a098108ea52">

위 XML에서 아래의 "memberRepository"가 위의 ref로 해서 생성자에 넘어간다. 실제 구현 객체는 MemoryMemberRepository이다.

할인 정책의 경우 id는 "discountPolicy", 클래스는 패키지명까지 다 해서 RateDiscountPolicy를 사용할 것이다. 그리고 위에다가 bean으로 "orderService"를 만들고 클래스는 패키지명까지 다 해서 OrderServiceImpl을 적는다. 그리고 contructor-arg로 해서 name은 "memberRepository"를 넣고, ref는 "memberRepository"로 넣는다. 또 constructor-arg로 name은 "discountPolicy", ref는 "discountPolicy"를 입력한다.

이렇게 해서 세팅이 다 끝났다. 형식이 XML 문서일 뿐, AppConfig.java와 완전히 똑같다.

이제 테스트를 실행하면, resourceLocations에 있는 "appConfig.xml"을 읽는다.

<img width="1684" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/9228459a-4f5d-463d-9cf4-357fa7e2f53d">

성공적으로 수행된다. 로그를 보면 'memberService', 'memberRepository', 'orderService', 'discountPolicy'가 singleton bean으로 등록되었다고 나온다.

<br>

GenericXmlApplicationContext를 만들고, 설정 정보가 클래스 파일에서 XML로 바뀐 것밖에 없다. 나머지 코드는 전부 똑같다. ApplicationContext를 그대로 쓰는 것이다.

XML 기반의 appConfig.xml 스프링 설정 정보와 코드로 된 AppConfig.java 설정 정보를 비교해보면 거의 비슷하다는 것을 알 수 있다. XML 기반으로 설정하는 것은 최근에 잘 사용하지 않으므로 이 정도로 마무리하고, 필요하면 스프링 공식 문서를 확인하면 되겠다.


## 8. 스프링 빈 설정 메타 정보 - BeanDefinition

이번에는 스프링 빈의 설정 메타 정보인 BeanDefinition에 대해서 알아보겠다.

스프링은 어떻게 이런 다양한 설정 형식을 지원하는 것일까? 그 중심에는 ```BeanDefinition```이라는 추상화가 있다. 쉽게 이야기해서 <b>역할과 구현을 개념적으로 나눈 것</b>이다.

* XML을 읽어서 BeanDefinition을 만들면 된다.
* 자바 코드를 읽어서 BeanDefinition을 만들면 된다.
* 스프링 컨테이너는 자바 코드인지, XML인지 몰라도 된다. 오직 BeanDefinition만 알면 된다. 이것을 기반으로 Bean을 생성하고 다 하는 것이다.

<br>

BeanDefinition을 빈 설정 메타정보라 한다.

* ```@Bean``` 혹은 XML에서 ```<bean>```을 하면 빈 하나당 각각 하나씩 메타 정보가 생성된다.

스프링 컨테이너는 이 메타정보를 기반으로 스프링 빈을 생성한다.

<img width="986" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f2300943-6388-44c7-8603-4fe085de2e98">

위 그림을 보면, 스프링 컨테이너가 BeanDefinition 정보를 가지고 동작한다고 보면 된다. 스프링 컨테이너 자체는 BeanDefinition에만 의존한다. 이것이 클래스로 설정된 정보인지, XML로 설정된 정보인지, 아니면 임의로 된 것인지 상관을 안 하는 것이다. 설계 자체를 추상화에만 의존하도록 한 것이다. BeanDefinition 자체가 인터페이스이다.

<img width="724" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/532d1c25-4fc3-4d2f-95cf-9f59fdc83c64">

<br>

코드 레벨로 조금 더 깊이 있게 들어가보자.

<img width="989" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/44982d3a-ae6f-4c47-ac2b-bc70e3986fec">

ApplicationContext를 구현한 AnnotationConfigApplicationContext가 있다. 이것은 들어가면 ```AnnotatedBeanDefinitionReader``` 이란 것이 있다.

<img width="1024" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/faef90d6-db34-4a69-9246-087ad16883ab">

이것이 ApplicationContext의 설정 정보인 AppConfig.class를 읽어서 BeanDefinition을 만들어낸다.

정리하면 다음과 같다.

* AnnotationConfigApplicationContext는 AnnotatedBeanDefinitionReader를 사용해서 AppConfig.class를 읽고 BeanDefinition을 생성한다.
* GenericXmlApplicationContext는 XmlBeanDefinitionReader를 사용해서 appConfig.xml 설정 정보를 읽고 BeanDefinition을 생성한다.
* 새로운 형식의 설정 정보가 추가되면, XxxBeanDefinitionReader를 만들어서 BeanDefinition을 생성하면 된다.


### BeanDefinition 살펴보기

<b>BeanDefinition 정보</b>

* BeanClassName: 생성할 빈의 클래스명(자바 설정처럼 팩토리 역할의 빈을 사용하면 없음)
* factoryBeanName: 팩토리 역할의 빈을 사용할 경우 이름. 예&#41; appConfig
* factoryMethodName: 빈을 생성할 팩토리 메서드 지정. 예&#41; memberService
* Scope: 싱글톤(기본값)
* lazyInit: 스프링 컨테이너를 생성할 때 빈을 생성하는 것이 아니라, 실제 빈을 사용할 때까지 최대한 생성을 지연처리 하는지 여부
* InitMethodName: 빈을 생성하고, 의존관계를 적용한 뒤에 호출되는 초기화 메서드 명
* DestroyMethodName: 빈의 생명주기가 끝나서 제거하기 직전에 호출되는 메서드 명
* Constructor arguments, Properties: 의존관계 주입에서 사용한다. (자바 설정처럼 팩토리 역할의 빈을 사용하면 없음)

<br>

BeanDefinitionTest를 생성해보겠다. 테스트 쪽에 beandefinition패키지를 생성하고, 그 안에 BeanDefinitionTest클래스를 생성한다. 코드는 다음과 같다.

```java
package hello.core.beandefinition;

import hello.core.AppConfig;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class BeanDefinitionTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
    @DisplayName("빈 설정 메타정보 확인")
    void findApplicationBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

            if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
                System.out.println("beanDefinitionName = " + beanDefinitionName + " beanDefinition = " + beanDefinition);
            }
        }
    }
}
```

우선 ```AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);```를 입력하여 ac를 생성하고, AppConfig.class를 설정 정보로 넣는다. 그리고 beanDefinition을 확인하기 위해 findApplicationBean을 만든다. 먼저 ```ac.getBeanDefinitionNames()```로 ac에서 beanDefinition의 이름들을 꺼낸다. 그리고 iter을 돌리면서 ac에서 definition 정보를 얻을 수 있다. 그 다음에 조건문을 넣어서, Role이 Application Role인 것만 출력하도록 한다. (안 그러면 너무 많이 출력되기 때문) beanDefinitionName과 beanDefinition 자체를 출력한다.

이제 실행하면 다음과 같다.

<img width="1685" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/6eba0aad-d843-4c7c-90a6-4b39698c90bc">

보면 먼저 memberService의 경우, scope는 할당이 안 되어 있으므로 싱글톤이라는 것으로 되는 것이다. lazy는 보통 스프링 빈들이 스프링 컨테이너가 뜰 때 등록이 되는데, 그게 아니라 나중에 실제 사용하는 시점에 스프링 빈을 초기화 하라는 정보이다. 그리고 autowireMode를 쓰는지 안 쓰는지 체크하는 것이 있다. 그리고 ```factoryBeanName=appConfig; factoryMethodName=memberService;```라는 것은 appConfig에 있는 memberService를 호출해서 실제 빈을 생성할 수 있구나라고 생각하면 된다. 그리고 ```defined in hello.core.AppConfig```로 되어 있다는 등의 정보들이 있다. (이것을 XML로 바꾸면 다르게 나올 것이다.)

<br>

이런 메타정보가 있고, 이 메타정보를 기반으로 실제 인스턴스를 생성할 수 있다.

정리하면 다음과 같다.

* BeanDefinition을 직접 생성해서 스프링 컨테이너에 등록할 수 있다. 하지만 실무에서 BeanDefinition을 직접 정의하거나 사용할 일은 거의 없다. → 어려우면 그냥 넘어가도 된다.
* BeanDefinition에 대해서는 너무 깊이있게 이해하기 보다는, 스프링이 다양한 형태의 설정 정보를 BeanDefinition으로 추상화해서 사용하는 것 정도만 이해하면 된다.
* 가끔 스프링 코드나 스프링 관련 오픈 소스의 코드를 볼 때, BeanDefinition이라는 것이 보일 때가 있다. 이때 이러한 메커니즘을 떠올리면 된다.

<br>

이번에는 BeanDefinitionTest의 코드에서 Xml을 사용해보겠다. 수정한 코드는 다음과 같다.

```java
package hello.core.beandefinition;

import hello.core.AppConfig;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

public class BeanDefinitionTest {

//    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    GenericXmlApplicationContext ac = new GenericXmlApplicationContext("appConfig.xml");

    @Test
    @DisplayName("빈 설정 메타정보 확인")
    void findApplicationBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

            if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
                System.out.println("beanDefinitionName = " + beanDefinitionName + " beanDefinition = " + beanDefinition);
            }
        }
    }
}
```

참고로 이전에 ```ApplicationContext ac = new ~~```로 ApplicationContext를 사용하지 못한 이유는, 뒤에 ```ac.getBeanDefinition(beanDefinitionName)```에서 getBeanDefinition을 못 하기 때문이다.

이번에는 ```GenericXmlApplicationContext ac = new GenericXmlApplicationContext("appConfig.xml");```를 입력해 XML을 사용한다. 실행하면 다음과 같이 나타난다.

<img width="1682" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/522221ca-bea9-490c-af4d-738463af3b82">

방금 했던 것과 좀 다르다. XML로 했을 경우, Generic bean이라고 해서 빈에 대한 정보가 명확하게 등록되어 있다. 그리고 ```defined in class path resource [appConfig.xml]```이라고 나와있다. 그리고 factoryBeanName, factoryMethodName 같은 것들이 다 빠져 있다. 이게 무슨 차이가 있냐면, 예전에는 다 XML로 설정하면서 이렇게 빈에 대한 클래스가 다 밖에 드러났다. 스프링이 빈을 등록하는 것이 두 가지이다. 첫 번째는 직접 스프링 빈을 스프링 컨테이너에 등록하는 방법이고, 두 번째는 우회해서 하는 것이다. 이 우회해서 하는 것이 factoryMethod라는 것을 쓰는 것이다. 그런데 이 자바 코드를 가지고 쓰는 방법은 factoryMethod를 통해서 등록하는 방법이다. 내가 생성했던 AppConfig라는 팩토리 메서드를 통해서, AppConfig가 제공하는 memberService라는 메서드를 통해서 제공하는 방식이다. AnnotationConfig로 오면서 외부에서 메서드를 호출해서 생성하는 팩토리 메서드 방식을 사용하게 된 것이다.

반대로 위 코드에서 ```AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);```를 선택해서 테스트를 돌려보면 다음과 같다.

<img width="1686" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7102b358-42c4-4537-b380-af4220fd9344">

factoryBeanName은 appConfig이고, factoryMethodName은 memberRepository 이렇게 나타난다. appConfig라는 팩토리 빈에서 이 팩토리 메서드를 통해서 생성이 된다고 보면 되는 것이다. AnnotationConfig를 사용하면 팩토리 빈을 통해서 등록되는 방식으로 스프링에 제공되는 것이다.

<br>

정리하면, <b>스프링은 BeanDefinition이라는 것으로 스프링의 메타 정보를 추상화한다</b>는 것만 기억하면 된다. 그리고 <b>스프링 빈을 만들 때에는 두 가지 방법이 있는데, 하나는 직접 스프링 빈을 등록하는 방법과, 두 번째는 팩토리 빈을 통해서 등록하는 방법</b>이다. 우리가 일반적으로 쓰는 것은 팩토리 빈을 통해서 등록하는 방식이라고 이해하면 된다.