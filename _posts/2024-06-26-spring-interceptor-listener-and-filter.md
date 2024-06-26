---
layout: post
title: Interceptor - Listener & Filter
categories: Spring-SpringBoot
date: 2024-06-26 23:48:00 +0900
---
Filter와 Interceptor의 내용에 대해 알아보겠다. 그 중에 먼저 Listener와 Filter에 대해 알아보겠다.

Listener에 대해서는 MVC를 공부할 때 알아본 적이 있다. root-context.xml에서 무언가 집어넣으려 할 때 리스너를 통해 등록한 적이 있다. Filter는 뭔지 모르겠지만 일상 생활에서도 너무 많이 사용하는 용어이다 보니, 무언가를 '거르는' 용도로 쓰이는 것을 예상할 수 있다. input이 filter를 통해서 바뀔 수 있다는 것을 생각해볼 수 있겠다.

## Listener

우선 Listener에 대해 알아보자. Listener의 특징들은 다음과 같다.

* 프로그래밍에서 Listener란 특정 이벤트가 발생하기를 기다리다가 실행되는 객체
* 이벤트란 특정한 사건 발생. Ex&#41; 버튼클릭, 키보드입력, 컨테이너 빌드 완료, 웹어플리케이션 시작, HTTP요청수신 등...
* 서블릿 컨테이너에서 발생하는 이벤트 감지
* web.xml 파일에 &#60;listener&#62; 태그를 이용하여 사용 가능
* 리스너가 여러 개일 경우 보통 선언된 순서대로 실행되지만 아닌 경우가 있으므로 각각의 리스너는 독립적으로 동작할 수 있도록 설계하는 것이 좋음