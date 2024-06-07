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
* ModelAndView → 요청처리를 하기 위해서 필요한 혹은 그 결과를 저장하기 위한 객체
* ViewResolver → Controller에 선언된 view이름을 기반으로 결과를 반환할 View를 결정
* View → 응답화면 생성

모든 요청과 모든 응답은 DispatcherServlet으로 시작해서 DispatcherServlet으로 끝난다고 보면 된다.

ModelAndView는 컨트롤러를 거치고 나온 객체인데, 요청 처리를 위해서 필요한 혹은 그 결과를 저장하기 위한 객체 즉, '<b>바구니</b>'이다. 이 바구니 안에 필요한 데이터들을 몽땅 담아서 '어디, 어떤 view로 보내주세요' 하고 이름을 넣는 바구니인 것이다.

View는 결국 응답 화면을 생성하는 JSP 이다.

<br>

## Spring MVC - 요청 처리 흐름

그래서 이 요청과 흐름은 이러한 방식으로 이뤄져 있다.

<img width="632" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/305f0770-fda2-4cc5-b6a4-5080587e3e98" alt="https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:rte2:ptl:spring_mvc_architecture">

1. Client의 요청이 들어오면 DispatcherServlet이 가장 먼저 요청을 받는다.
2. HandlerMapping이 요청에 해당하는 Controller를 return한다.
3. Controller는 비즈니스 로직을 수행(호출)하고 결과 데이터를 ModelAndView에 반영하여 return한다.
4. ViewResolver는 view name을 받아 해당하는 View 객체를 return한다.
5. View는 Model 객체를 받아 rendering한다.

그런데 HandlerMapping에서 나온 결과가 Controller라 되어 있는데, 인터넷을 검색해보면 한 depth가 더 있다. HandlerAdapter라는 것이 있다. HandlerApdater를 호출하고 그 HandlerAdapter가 Controller(핸들러)를 호출한다.

그래서 HandlerMapping이라는 것은 결국 어떤 컨트롤러에 가야 하는지 알려주는 것이다. 그래서 DispatcherServlet까지 다시 돌아왔다.

DispatcherServlet은 이제 사용자가 어떤 요청을 보냈는데, 어떤 컨트롤러가 처리하면 될지에 대한 정보까지 있다. 이것을 통해서 해당 컨트롤러에게 실제로 요청을 보낸다. 그러면 컨트롤러는 내부적으로 서비스를 호출하고, 리포지토리를 통해서 필요하다면 DB까지 다녀와서 처리를 한다. (Model과 소통을 하는 것이다.)

그러고 나서 Controller에는 ModelAndView라는 객체가 튀어 나오는데, Model이라고 하는 파트에서는 데이터에서 필요한 정보들을 저장할 것이고, View라는 것에서는 어떤 View에 가면 될지를 알려줘야 하니까 어떤 view로 가면 되는지에 대한 view 이름을 담아서 'ModelAndView'라는 객체를 DispatcherServlet에게 돌려준다.

이제 DispatcherServlet은 ModelAndView 객체에서 'view'의 이름만 알고 있다. 그 view의 이름을 ViewResolver에게 보낸다. ViewResolver는 view 이름을 가지고 '아 이건 어떤 경로에 있고 어디에 가면 확장자는 어떤 것이고 ~'를 다 가지고 있다. View 이름만을 가지고 확장자를 붙이는 것이다.

예전에 'WEB-INF/regist.jsp'와 같은 방식으로 직접 경로를 명시했다면, 이제는 'view 이름은 regist 야' 까지만 ModelAndView에 담아서 보내는 것이다. 그러면 ViewResolver라는 것이 regist라고 하는 이름을 보고 실제 경로인 WEB-INF/ 뒤에 붙이고, 확장자인 .jsp를 붙여서 DispatcherServlet에게 돌려준다.

이제 DispatcherServlet에는 어떤 모델, 어떤 정보를 가지고 있는지와, 그리고 view 파일이 어디에 가면 있는지를 다 알고 있다. 그래서 View 파일로 넘어간다.

그러면 JSP를 통해서 모델에서 받은 객체로 쭉 작성을 해버리는 것이다. 작성이 끝났으면 response로 DispatcherServlet에게 보내고, 다시 그 response를 client에게 쏴버린다.

<br>

위와 같은 일련의 과정을 다시 정리하면 다음과 같다.

1. 클라이언트 요청이 들어오면 DispatcherServlet이 받는다.
2. HandlerMapping이 어떤 Controller가 요청을 처리할지 결정한다.
3. DispatcherServlet은 Controller에 요청을 전달
4. Controller는 요청을 처리한다.
5. 결과(요청 처리를 위한 data, 결과를 보여줄 view의 이름)를 ModelAndView에 담아 반환
6. ViewResolver에 의해서 실제 결과를 처리할 View를 결정하고 반환
7. 결과를 처리할 View에 ModelAndView를 전달
8. DispatcherServlet은 View가 만들어낸 결과를 응답

<br>

## Spring MVC (DispatcherServlet)

DispatcherServlet이라고 하는 것은 설정 파일이 필요하다. 이전에는 설정 파일로 applicationContext.xml 라는 파일을 한 개 만들었다. 그런데 DispatcherServlet에는 설정 파일이 두 개 필요하다.

이름은 관례적으로 servletContext.xml, rootContext.xml 로 짓는다. servletContext.xml 에는 어떤 것들을 빈으로 관리를 하고 있을까? Controllers, ViewResolver, HandlerMapping을 servletContext에서 빈으로 관리한다. rootContext에서는 Services, Repositories 등을 관리한다.
