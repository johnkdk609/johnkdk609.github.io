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