---
layout: post
title: Spring Container Build
categories: Spring-SpringBoot
date: 2024-04-28 22:26:00 +0900
---
Spring Container Build에 대해서 알아보겠다.

## Spring IoC Container

스프링에서 핵심적인 역할을 하는 객체를 <b>Bean</b>이라고 하며, <b>Spring Container는 Bean의 인스턴스화 조립, 관리의 역할, 사용 소멸에 대한 처리를 담당한다.</b> '인스턴스화 조립'이란 ```new ~~~``` 해서 인스턴스를 생성해 의존성을 알아서 주입해준다는 것이다. '사용 소멸'이란 생명주기(LifeCycle)에 관여한다는 것이다.

* BeanFactory (interface)
    - 프레임워크 설정과 기본 기능을 제공하는 컨테이너
    - 모든 유형의 객체를 관리할 수 있는 메커니즘 제공

* ApplicationContext (interface)
    - BeanFactory 하위 인터페이스
    - 이벤트 처리, 국제화용 메세지 처리 등 다양한 기능 제공

* WebApplicationContext (interface)
    - 웹 환경에서 Spring을 사용하기 위한 기능이 추가됨
    - 대표적인 구현 클래스로 XmlApplicationContext가 있음

BeanFactory를 그대로 받아서 만든 ApplicationContext, 그리고 그 ApplicationContext에서 좀 더 나아가서 만든 WebApplicationContext 등이 있다. 이런 것들을 가져와서 처리할 것이다.

<br>

## 스프링 설정 정보 (Spring configuration metadata)

그래서 스프링 설정 정보를 쓸 수 있어야 한다.

스프링 설정 정보란 어플리케이션 작성을 위해 생성할 Bean과 설정 정보, 의존성 등의 방법을 나타내는 정보이다. 이러한 설정 정보를 작성하는 방법에는 XML 방식, Annotation 방식, Java Config 방식이 있다.

(나는 XML 방식과 Annotation 방식을 중점적으로 다뤄볼 것이다. 최신 트렌드는 Java Config 방식인데, 이는 Spring Boot를 다룰 때 알아볼 것이다.)