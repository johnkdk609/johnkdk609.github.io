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