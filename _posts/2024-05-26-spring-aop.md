---
layout: post
title: Spring AOP
categories: Spring-SpringBoot
date: 2024-05-26 05:33:00 +0900
---
그러면 스프링에서는 AOP를 어떻게 설정할 수 있을까?

앞서 이론을 쭉 익혔으니, 스프링에서 어떻게 쓸 수 있을지 알아보겠다.

## Advice Type

* before - target 메서드 호출 이전
* after - target 메서드 호출 이후, java exception 문장의 finally와 같이 동작
* after returning - target 메서드 정상 동작 후
* after throwing - target 메서드 에러 발생 후
* around - target 메서드의 실행 시기, 방법, 실행 여부를 결정

우선 스프링에서는 Join Point가 될 수 있는 것들은 모두 메서드들이었다. 내가 지금 target 메서드를 실행하는 시점 이전 시점이 before 이다.

그리고 after라는 것은 target 메서드 호출 이후에 finally 처럼 동작하는 것이다.

after returning, after throwing은 동시에 동작할 수 없다는 특징을 지닌다.

around은 앞의 것들을 정의한 후에 around에 몽땅 쓸 수도 있고, 아니면 각자 동작을 하게끔 쓸 수도 있는 등... 전반적인 설정이다.

<br>

## Spring AOP Proxy

스프링에서는 실제 기능이 구현된 target 객체를 호출하면, target이 호출되는 것이 아니라 advice가 적용된 Proxy 객체가 호출된다.

가령, "프로그래머야 코딩 부탁해~~" 라고 메서드를 호출했을 때, PersonProxy라는 것이 동작을 한다. 그런데 Spring에서는 이러한 것들을 자동으로 해준다.

<img width="500" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b7f0833c-e3ba-4cd6-a7fd-d5024b675d46">

* Spring AOP는 기본적으로 표준 JDK dynamic proxy를 사용한다.

* 인터페이스를 구현한 클래스가 아닌 경우 CGLIB 프록시를 사용한다.