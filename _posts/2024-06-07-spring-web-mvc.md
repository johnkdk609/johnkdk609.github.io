---
layout: post
title: Spring Web MVC
categories: Spring-SpringBoot
date: 2024-06-07 19:07:00 +0900
---
프레임워크를 배운다는 것은, 프레임워크가 어떻게 만들어져 있는지를 공부하는 것이 아니라, 만들어진 프레임워크를 사용하는 방법에 대해서 익히는 것이다.

스프링에서는 Web MVC 를 어떻게 사용하고 있는지 살펴보자.

## Spring Web MVC

* Servlet API를 기반으로 구축된 Web Framework
* 정식 명칭은 Spring Web MVC 이지만, Spring MVC로 주로 알려져 있음
* Spring Framework 이 제공하는 DI, AOP 뿐 아니라, WEB 개발을 위한 기능을 제공
* DispatcherServlet(Front-Controller)를 중심으로 디자인 되었으며, View Resolver, Handler Mapping, Controller 와 같은 객체와 함께 요청을 처리하도록 구성됨

내가 만약 스프링을 하려면, 스프링 내부적으로 Spring Boot, Spring Data, Spring Security 등.. 매우 많다. Spring Web MVC는 그 중의 하나이다.

<br>

## Spring MVC 구성요소

* DispatcherServlet → 클라이언트 요청 처리 (요청 및 처리 결과 전달)
* HandlerMapping → 요청을 어떤 Controller 가 처리할 지 결정
* Controller → 요청에 따라 수행할 메서드를 선언하고, 요청처리를 위한 로직 수행 (비즈니스 로직 호출)
* ModelAndView → 요청처리를 하기 위해서 필요한 혹은 그 결과를 지정하기 위한 객체
* ViewResolver → Controller에 선언된 view이름을 기반으로 결과를 반환할 View를 결정
* View → 응답화면 생성

