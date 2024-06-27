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

Listener는 "귀를 연다"로 생각하면 된다. 자바스크립트에서 버튼 클릭을 했을 때 eventListener을 쓸 수 있는데, 마찬가지로 자바에서도 이런 리스너를 쓸 수 있다. 이때 '특정 이벤트'란 버튼을 클릭했다든지, 키보드를 다 쳤다든지.. 가 될 수 있겠다.

컨테이너 빌드가 완료되었다는 것은, 무언가 실행을 했을 때 서블릿 컨테이너라든지, IoC 컨테이너라든지가 완성이 되었다면 이것을 하나의 이벤트라고 볼 수 있는 것이다.

리스너1, 리스너2를 선언했을 때, 두 번째 리스너는 첫 번째 리스너가 처리된 이후에 하는 느낌으로 선언을 할 수 있다. 하지만 아닌 경우도 있다. 어떤 환경에 따라서 리스너2가 먼저 실행될 수도 있는 것이다. 그러므로 각각의 리스너는 독립적으로 동작할 수 있게 설계하는 것이 좋다.

<br>

## Listener 사용 (Annotation)

Listener를 사용하는 방법은 Annotation을 사용하는 방법과, web.xml에 등록하는 방법의 두 가지 방법이 있다.

그러면 바로 코드를 보면서 얘기해보겠다. (지금 하는 것은 스프링과 관련이 따로 없다.)

STS에서 "Spring_04_Listener_Filter"라는 이름으로 Dynamic Web Project를 생성한다. Next를 여러 번 클릭하고 "Generate web.xml"에 체크하고 Finish를 클릭한다.

이제 Project Explorer 쪽에서 src/main/java에 우클릭을 하고 Create Listener를 클릭한다. package이름은 "com.ssafy.mvc.listener"로 하고, 클래스 이름은 "MyListener1"으로 한다. 그리고 Next를 클릭하면 각종 인터페이스를 선택하는 화면이 나타난다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/91fff213-eb50-4878-bddb-f139971dc1c1" width="450px" />

Servlet context events 와 관련된 것을 처리하려면 위 두개를 체크하면 되고, HTTP session events와 관련된 것을 처리하려면 그와 관련된 것들을 체크하면 되며, Servlet request events와 관련된 것을 처리하려면 또 그와 관련된 것들을 체크하면 된다. (직접 작성해도 된다.) 이 경우에는 맨 위의 Lifecycle을 하나 체크하고 Next를 클릭한다. 그리고 "Inherited abstract methods"에 체크된 것을 유지한 채 Finish를 클릭한다.

그러면 추상 메서드들이 작성되어 있는 MyListener1 이 생성된다.