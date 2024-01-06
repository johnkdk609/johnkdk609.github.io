---
layout: post
title: 컴포넌트 스캔
description: 스프링 핵심 원리 - 기본편
categories: Spring-SpringBoot
date: 2024-01-04 23:27:00 +0900
---
이번 시간부터는 컴포넌트 스캔에 대해 알아볼 것이다. 컴포넌트 스캔과 의존관계 자동 주입이 연결된 부분이 있다.

## 목차

1. 컴포넌스 스캔과 의존관계 자동 주입 시작하기
2. 탐색 위치와 기본 스캔 대상
3. 필터
4. 중복 등록과 충돌

## 1. 컴포넌트 스캔과 의존관계 자동 주입 시작하기

지금까지 스프링 빈을 등록할 때에는, 자바 코드의 ```@Bean```, XML의 ```<bean>``` 등을 통해서 설정 정보에 직접 등록할 스프링 빈을 나열했다. 예제에서는 몇 개가 안 되었지만, 이렇게 등록해야 할 스프링 빈이 수십, 수백개가 되면 일일이 등록하기도 귀찮고, 설정 정보도 커지고, 누락하는 문제도 발생한다. 역시 개발자는 반복을 싫어한다. (무엇보다 귀찮다.)

그래서 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 컴포넌트 스캔이라는 기능을 제공한다.

또 의존관계도 자동으로 주입하는 ```@Autowired```라는 기능도 제공한다.

<br>

코드로 컴포넌트 스캔과 의존관계 자동 주입을 알아보자.

먼저 기존 AppConfig.java는 과거 코드와 테스트를 유지하기 위해 남겨두고, 새로운 AppConfig.java를 만들어보겠다. hello.core 패키지 안에 AutoAppConfig클래스를 생성한다. <b>AutoAppConfig클래스</b>의 코드는 다음과 같다.

```java
package hello.core;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;

@Configuration
@ComponentScan(
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {

}
```

우선 여기까지 코드를 작성했다. 우선 설정 정보이니 ```@Configuration```을 적는다. 컴포넌트 스캔은 스프링 빈을 긁어서 자동으로 스프링 빈으로 끌어올려야 한다. 그래서 ```@ComponentScan```이라는 어노테이션을 쓴다. 이 컴포넌트 스캔은 ```@ComponentAnnotation```이 붙은 클래스를 찾아서 자동으로 스프링 빈으로 등록해준다. 그런데 먼저, 괄호를 열어서 ```excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)```를 입력한다. 이 코드는 컴포넌트 스캔으로 다 뒤져서 스프링 빈으로 자동 등록하는데, 그 중에서 뺄 것을 지정해주는 것이다. 타입을 지정하고, ```Configuration.class```를 뺄 것이다. 이 어노테이션이 붙은 것은 뺄 것이라는 것이다.

AppConfig는 자동으로 등록되면 안 된다. 지금 수동으로 등록하는데 이러면 충돌이 날 것이다. ```@Configuration``` 어노테이션에 ```Alt```를 누른 채 클릭해서 들어가보면, ```@Component```라는 것이 붙어있다. 

<img width="785" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/57895d9d-6ba9-4298-842e-cb8052528168">

그래서 얘도 자동 스캔의 대상이 되어버린다. 그래서 일단은 예제를 안전하게 유지하기 위해서 exclude를 한 것이다.

<br>

여기까지 한 것을 정리하면 다음과 같다.

* 컴포넌트 스캔을 사용하려면 먼저 ```@ComponentScan```을 설정 정보에 붙여주면 된다.
* 기존의 AppConfig와는 다르게 ```@Bean```으로 등록한 클래스가 하나도 없다!

(참고: 컴포넌트 스캔을 사용하면, ```@Configuration```이 붙은 설정 정보도 자동으로 등록되기 때문에, AppConfig, TestConfig 등 앞서 만들어두었던 설정 정보도 함께 등록되고, 실행되어 버린다. 그래서 ```excludeFilters```를 이용해서 설정정보는 컴포넌트 스캔 대상에서 제외했다. 보통 설정 정보를 컴포넌트 스캔 대상에서 제외하지는 않지만, 기존 예제 코드를 최대한 남기고 유지하기 위해서 이 방법을 선택했다.)

<br>

컴포넌트 스캔은 이름 그대로 ```@Component``` 어노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록한다. ```@Component```를 붙여주자.

(참고: ```@Configuration```이 컴포넌트 스캔의 대상이 된 이유도, ```@Configuration``` 소스코드를 열어보면 ```@Component``` 어노테이션이 붙어있기 때문이다.)

<br>

이제 각 클래스가 컴포넌트 스캔의 대상이 되도록 ```@Component``` 어노테이션을 붙여주자.

<br>

우선 MemoryMemberRepository클래스에 ```@Component```를 붙인다. 이제 빈 등록을 안 해도 된다.

<img width="585" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/3a5a30c9-8c15-453f-b441-71e8feef9f4a">

그 다음에 사용하기로 한 RateDiscountPolicy클래스에도 ```@Component```를 붙여준다.

<img width="531" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/392df765-0f85-4967-9359-009d98878495">

그리고 MemberServiceImpl도 스프링 빈으로 등록해야 되니까, ```@Component```를 붙여준다.

<img width="526" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ae9e61e2-08d3-43a8-9bae-2e470c40b794">

<br>

여기까지 했는데, 한 가지를 더 해줘야 한다. MemberServiceImpl의 경우, 내가 스프링 빈으로 등록하는 것이 아니다. 자동으로 스프링 빈으로 등록이 되는 것이다. 그러면 의존관계 주입은 어떻게 할 것인가? 기존에 AppConfig에서는 MemberService는 memberRepository() 의존관계를 주입할 것이라고 딱 명시할 수 있었다.

<img width="583" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/a2f45d81-c90b-42a7-afe8-5f539b5c5a0a">

그런데, AutoAppConfig를 보면, 아무것도 없다. 그냥 MemberServiceImpl이 떡하니 스프링 빈으로 등록되는 것이다. 그러면 의존관계 주입을 해줄 수 있는 방법이 없다.

그래서 자동 의존관계 주입이 필요하다.

<br>

의존관계를 자동으로 주입해주는 ```@Autowired```라는 기능을 생성자에 붙여주면, 스프링이 여기에다가 MemberRepository 타입을 만든 것을 찾아와서 의존관계를 자동으로 주입해서 연결해주는 것이다.

<img width="588" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/46614b1d-c21d-4335-8ef1-6cc8992cb63a">

그래서 컴포넌트 스캔을 쓰게 되면, 내 빈이 자동으로 등록이 되는데 의존관계를 설정할 수 있는 방법이 없다. 왜냐하면 수동으로 등록할 수 있는 장소가 없는 것이다. 그래서 ```@Autowired```를 쓰게 되면 스프링이 ```MemberServiceImpl```도 스프링 빈으로 등록해주고, MemberRepository 타입에 맞는 memoryMemberRepository 스프링 빈을 딱 주입해주는 것이다. 마치 ```ac.getBean(MemberRepository.class)```의 방식으로 동작한다고 이해하면 된다. 자동으로 이 코드가 들어간다고 보면 되는 것이다.

<br>

지금까지 한 것을 정리해보면 다음과 같다.

* 이전에 AppConfig에서는 ```@Bean```으로 직접 설정 정보를 작성했고, 의존관계도 직접 명시했다. 이제는 이런 설정 정보 자체가 없기 때문에, 의존관계 주입이 이 클래스 안에서 해결해야 한다.
* ```@Autowired```는 의존관계를 자동으로 주입해준다. (자세한 룰은 조금 뒤에 나온다.)