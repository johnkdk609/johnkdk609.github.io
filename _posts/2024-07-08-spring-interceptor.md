---
layout: post
title: Interceptor
categories: Spring-SpringBoot
date: 2024-07-08 21:58:00 +0900
---
Interceptor에 대해서 살펴보겠다.

공놀이의 예를 들어보자. A와 B가 공을 주고 받고 있을 때, C가 나타나서 중간에서 공을 빼앗으려 하는 것이다. 여기서 공을 빼앗은 행위를 'intercept'라고 한다. 

인터셉트라는 것은 사용자가 요청을 던졌을 때, 그것을 가로채서 처리하고 다시 돌려주는 것이다. 필요하다면 그냥 빼앗아서 없애 버리기도 한다.

이렇게 보면 필터와 비슷해 보인다. Interceptor는 Filter의 상위 버전이다. 이때 상위 버전이란 '위에 있다'는 것이 아니라 기능이 좀 더 많다는 뜻이다.

<br>

## Interceptor

Interceptor의 특징은 다음과 같다.

* HandlerInterceptor를 구현한 것
* 요청(request)을 처리하는 과정에서 요청을 가로채서 처리
* 접근 제어(Auth), 로그(Log) 등 비즈니스 로직과 구분되는 반복적이고 부수적인 로직 처리
* HandlerInterceptor의 주요 메서드
    - preHandle()
    - postHandle()
    - afterCompletion()

'HandlerInterceptor를 구현한 것'이라는 것을 통해 HandlerInterceptor가 인터페이스라는 것을 추론할 수 있다.

어떤 게시판 서비스가 있을 때, 여기에는 조회, 등록, 수정, 삭제 등의 기능들이 있다고 가정해보자. 게시글 조회까지는 아무나 할 수 있지만, 등록, 수정, 삭제 요청은 아무나 하면 안 된다. 이러한 요청이 넘어왔을 때에는 Interceptor가 이 요청을 가로채서 "너 로그인 했어?" 라고 물어보고, 로그인을 안 했으면 그 요청을 없애버리는 것이다. 이러한 것을 'Interceptor'라고 한다.

이러한 인터셉터는 주요 메서드가 3가지가 있다. preHandle()은 이전에 해주는 것, postHandle()은 요청이 끝나고 나서, afterCompletion()은 모든 게 끝나고 나서 진행되는 것으로 짐작할 수 있다.

<br>

그러면 요청을 하나씩 뜯어 보겠다.