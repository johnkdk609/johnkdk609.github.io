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