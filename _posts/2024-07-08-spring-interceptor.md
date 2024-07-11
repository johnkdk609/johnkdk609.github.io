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

request, response가 있고, 어떤 핸들러로 메서드를 실행시키기 위해 handler가 인자로 있다. 그런데 추가로 modelAndView가 인자로 들어왔다. DispatcherServlet 에서 컨트롤러를 찍고, 필요하다면 무언가를 처리하고 다시 돌아올 때 ModelAndView를 담아서 가져온다. 담아왔던 view이름을 가지고 ViewResolver에게 "이거 view 어디 있나요?" 하고 물어보는 것이다.

postHandle이라는 것은 컨트롤러를 찍고 온 것이기 때문에 ModelAndView 객체를 쓸 수 있다. 만들어져 있으니까. preHandle은 없다.

정상적으로 실행했을 때 추가 기능이 필요하다면 이렇게 구현할 수 있다.

만약 Controller에서 예외가 터졌으면, 정상적으로 요청의 흐름이 가지 않았다는 것이니 해당 메서드는 실행되지 않는다. 그러니까 postHandle은 예외 없이 정상적으로 실행이 되었을 때 실행이 되는 것이다.

<br>

### afterCompletion

```java
@Override
public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception e)
        throws Exception {
```

* 뷰가 클라이언트에게 응답을 전송한 뒤 실행
* Controller 에서 예외 발생 시, 네 번째 파라미터로 전달이 된다. (기본은 null)
* 따라서 Controller에서 발생한 예외 혹은 실행 시간 같은 것들을 기록하는 등 후처리 시 주로 사용.

모든 게 끝나고 나서 실행되는 메서드이다. request, response가 모두 OK이니까 (사용자에게 다 줘버리고 난 후이니까) ModelAndView는 없다. 그래도 어떤 handler에서 왔는지는 알려준다. 그리고 마지막으로 Exception 객체가 네 번째 파라미터로 있다.

만약 예외가 발생하지 않으면 이 Exception 파라미터는 null 이다. 예외가 만약 터졌다면 이제는 어떤 예외 객체를 가지고 있는 것이다.

postHandle은 예외가 터졌을 때 정상적으로 실행되지 않지만, afterCompletion은 무조건 실행이 된다. 그래서 Controller에서 발생한 예외 혹은 실행 시간 같은 것들을 기록하는 등 후처리 시 주로 사용되는 것이다.

그런데 우리가 코드를 짜면서 실제로는 preHandle만 주로 쓰게 될 것이다. postHandle, afterCompletion을 생각보다 쓸 일이 없다. 쓸 곳이 없다. 서비스를 가지고 실행 시간 등을 기록할 것이 아니기 때문이다. 하지만 알고 있어야 한다.

<br>

### Interceptor 흐름

그래서 Interceptor의 흐름을 도식화하면 다음과 같다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/d821493c-7338-47f6-90d9-798f77ba1026" width="550px" />

DispatcherServlet에서 요청이 들어왔을 때, 제일 먼저 컨트롤러로 가기 전에 preHandle라는 (인터페이스를 구현한) 인터셉터가 먼저 메서드를 실행한다. 그리고 여기서 true 혹은 false 를 반환한다. false이면 Controller로 가지 않는다. true인 경우에만 DispatcherServlet에서 Controller로 진행을 한다.

진행을 하고 무언가 응답을 받았다. 만약 이 상황이 정상적으로 동작 하였다면 postHandle을 실행한다. 그런데 만약 Controller에서 DispatcherServlet으로 가는 진행 상황이 정상적으로 동작하지 않고 예외가 터지면 postHandle을 실행하지 않는다.

그리고, (위에 다 모르겠고) 사용자가 View 까지 전달된 이후에 정상적으로 실행이 되었다면 afterCompletion이 OK이다. 이때 네 번째 인자의 Exception은 null 이다. 무언가 예외가 터졌더라도 afterCompletion은 처리가 된다. 이때 네 번째 인자인 Exception은 null이 아니다.

<br>

### Interceptor 등록 (Servlet-context.xml)

* &#60;interceptors&#62; 태그 안에 &#60;interceptor&#62; 태그로 등록 가능
* 매핑할 URL을 지정할 수 있고, 제외할 URL 또한 지정할 수 있음.

```xml
<interceptors>
    <interceptor>
        <mapping path="/*"/>
        <beans: bean class="com.ssafy.mvc.interceptor.AInterceptor"/>
    </interceptor>
</interceptors>
```

이제 Interceptor를 등록해야 하는데, Interceptor은 web과 관련이 있을까 없을까? 관련이 있다. 왜냐하면 요청이 오면서 그 요청을 가로채서 무언가 처리를 하는 것이기 때문이다.

그러면 MVC에서, DispatcherServlet에서는 설정 파일을 두 개 가지고 한다고 했다. servlet-context.xml 과 root-context.xml 중 Interceptor은 web과 관련돼 있으니 Servlet-context.xml에 등록해야 한다.

매핑할 URL을 지정하고, 제외할 URL을 지정할 수 있다. 어떠한 요청이 왔을 때에만 이 Interceptor가 동작하게 할 수 있고, 모든 요청을 다 처리할 것인데 이러이러한 것은 처리해 주라고 제외를 할 수 있다.

스프링에서 사용하는 PathPattern은 <a href="https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/util/pattern/PathPattern.html" alt="Spring PathPattern">해당 링크</a>를 참고하면 된다.

<br>