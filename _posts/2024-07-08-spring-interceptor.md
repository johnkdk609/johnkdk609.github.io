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

### preHandle

```java
@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
        throws Exception {
```

* Controller(핸들러) 실행 이전에 호출
* true를 반환하면 계속 진행한다.
* false를 반환하면 요청을 종료한다.

preHandle의 반환값이 boolean 형이기 때문에, true or false를 반환한다. 이때 false를 반환하면 그 즉시 요청을 종료하는데, 바로 종료하는 게 아니라 어딘가로 페이지를 돌려버리는(이동시켜버리는) 것이다. Ex&#41; 로그인이 필요한 서비스였다면 로그인 페이지로 돌리는 것이다.

그래서 인자로는 request, response가 들어오고, handler라고 하는 '지금 내가 어디 컨트롤러로 가야 하는지 알 수 있는' 객체도 들어온다.

<br>

### postHandle

```java
@Override
public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
        ModelAndView modelAndView) throws Exception {
```

* Controller(핸들러) 실행 후 호출
* 정상 실행 후 추가 기능 구현 시 사용
* Controller에서 예외 발생 시 해당 메서드는 실행되지 않음

request, response가 있고, 어떤 핸들러로 메서드를 실행시키기 위해 handler가 인자로 있다. 그런데 추가로 modelAndView가 인자로 들어왔다. 디패서에서 컨트롤러를 찍고, 필요하다면 무언가를 처리하고 다시 돌아올 때 ModelAndView를 담아서 가져온다. 담아왔던 view이름을 가지고 ViewResolver에게 "이거 view 어디 있나요?" 하고 물어보는 것이다.

postHandle이라는 것은 컨트롤러를 찍고 온 것이기 때문에 ModelAndView 객체를 쓸 수 있다. 만들어져 있으니까. preHandle은 없다.

정상적으로 실행했을 때 추가 기능이 필요하다면 이렇게 구현할 수 있다.

만약 Controller에서 예외가 터졌으면, 정상적으로 요청의 흐름이 가지 않았다는 것이니 해당 메서드는 실행되지 않는다. 그러니까 postHandle은 예외 없이 정상적으로 실행이 되었을 때 실행이 되는 것이다.

<br>

