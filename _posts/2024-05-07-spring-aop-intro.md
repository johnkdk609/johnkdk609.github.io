---
layout: post
title: 관점 지향 프로그래밍
categories: Spring-SpringBoot
date: 2024-05-07 17:51:00 +0900
---
이번에는 관점 지향 프로그래밍 (AOP)에 대해서 알아보겠다.

그 전에 앞서 알아본 의존성 주입(Dependency Injection)에 대해서 한 번 정리해보자. Programmer는 Computer가 필요했는데, 고용이 될 때마다 컴퓨터를 가져와서 일을 하는 게 아니라 Spring이라고 하는 업체의 사장님이 Programmer을 고용할 때 이미 가지고 있던 Computer를 집어넣어주는 것이다. 이것이 의존성 주입의 개념이다.

의존성 주입을 하는 데에는 크게 3가지 방법이 있었다. (밑줄 친 것들 세 개)

1. XML 설정 파일 쓰는 법
    1. <u>직접 빈 등록</u>
    2. <u>@Component 스캔</u>
2. <u>Java 설정 파일을 쓰는 법</u>
    1. @Bean 직접 등록
    2. @스캔

<br>

## 관점 지향 프로그래밍

관점 지향 프로그래밍을 하기 전에, 객체지향프로그래밍인 OOP(Object Oriented Programming)에 대하여 "OOP is A PIE" 라는 말이 있다. 각각의 객체지향의 특징을 따고 있는 알파벳의 앞 글자들이다. <b>Abstract(추상화)</b>, <b>Polymorphism(다형성)</b>, <b>Inheritance(상속)</b>, <b>Encapsulation(캡슐화)</b>의 네 가지 특징이다.

또 알면 좋은 것은 객체지향 설계의 5원칙인 SOLID이 있다. 객체지향 프로그래밍은 하나의 프로그래밍 방법론이었다.

<br>

