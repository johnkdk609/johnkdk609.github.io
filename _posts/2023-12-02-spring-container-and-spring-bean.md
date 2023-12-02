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