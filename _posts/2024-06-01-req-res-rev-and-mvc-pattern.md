---
layout: post
title: 요청과 응답 흐름 복습 & MVC 패턴
categories: Spring-SpringBoot
date: 2024-06-01 15:21:00 +0900
---
Spring MVC에서 MVC는 Model, View, Controller 이다.

<br>

## 요청과 응답 흐름 복습

MVC 패턴에 들어가기 앞서서, 요청과 응답의 흐름에 대해 복습해보자.

클라이언트, 서버가 있을 때, 일단 우리는 Server로 WAS를 사용하기로 했다. 그리고 WAS로는 tomcat을 사용하기로 했다.

클라이언트가 서버에게 보내는 것을 request라고 하고, 반대로 응답 받은 것을 response라고 하였다. 어떤 요청을 날렸을 때, 누가 받아서 제일 처음 처리를 하는 것이었는가?

가령 "내가 ~~ 한 /main 경로로 보낼래" 하면 이 /main 으로 매핑되어 있는 servlet이 동작을 하였다. 이 servlet은 여러 개가 있을 수 있다. /regist, /user 등 서블릿을 여러 개 만들 수 있는데, 우리는 서블릿을 한 개만 쓰기로 했다. 이러한 방식을 "<b>Front Controller Pattern</b>"이라고 부른다. 서블릿을 한 개만 두면서 모든 요청을 이 /main 이 처리하게끔 한 것이다.

모든 요청이 /main 으로 들어온다면, 그 안에서 "이것은 등록이에요", "이것은 삭제에요" 등.. 의 요청들을 구분을 할 수 있어야 한다. 이것들을 구분하기 위해서 action이라고 하는 key에 내가 실제로 수행할 동작을 넣고 사용자에게 보냈다.

그러니까 request를 보낼 때, "action은 ~~야" 의 방식으로 같이 낑겨서 보낸 것이다. 이런 것을 "hidden tag"라고 부른다. "action"은 그냥 우리가 정의한 이름일 뿐이지, 꼭 action일 필요는 없었다.

그리고 이 안에서 함수들을 쭉 정의했다. 가령 등록은 "doRegist", 조회는 "doList" 등으로 한 것이다. 이렇게 몽땅 해서 JSP에다가 넘겨줬다. JSP는 Java Server Pages의 약자이다. HTML 코드 안에다가 자바 코드를 넣는 방식이다. 여기서는 동적으로 데이터를 받을 수 있다.

그래서 action에서 얻은 데이터들을 JSP에 보내기로 했다. JSP로 데이터를 보내는 방식을 "페이지 이동"이라고 한다. 페이지 이동 방식에는 크게 두 가지가 있다. 

1. 포워딩 (forwarding)
2. 리다이렉팅 (redirecting)

우선 <b>포워딩</b>은 내가 내 요청과 응답의 객체를 그대로 가져와서 JSP에게 토스하는 것이다. request가 유지되기 때문에 요청 통로에 무언가 담아놨다면 이것 또한 JSP에서 사용할 수 있다.

<b>리다이렉팅</b>은 클라이언트에게 가령 "/main?action=list 로 요청을 다시 보내"라고 하는 것이다. 즉, 리다이렉팅은 클라이언트에게 "~~ 요청을 네가 다시 보냈으면 좋겠는데" 하는 것이다. 그래서 응답(response)에 이러한 내용이 담겨서 갈 것이고, 사용자는 다시 doList 요청을 보내는 것이다. 이때 상태 메세지로 300번대가 날라간다. 새롭게 요청을 보내는 것이기 때문에 request와 response를 재활용할 수 없다. 내가 담아둔 것을 재활용할 수 없는 것이다.

이 차이를 명확히 알고 있어야 한다.

<br>

HTTP 통신은 되도록이면 연결을 끊어버리고 상태를 저장하지 않는다고 했는데, 그래서 등장한 개념이 <b>쿠키(Cookie)</b>를 이용해서 처리를 하는 것이다.

이 쿠키를 통해서 사용자의 간단한 정보를 사용자 브라우저에 저장을 할 것이고, 그리고 어떤 요청이 들어왔는데 "너 우리 사이트 왔는데 세션 아이디 없네" 하면 클라이언트를 위해서 메모리 공간을 할당하고 여기에 들어올 수 있는 주소(Hash 값)를 key 값으로 쿠키에 담아서 응답에 실어서 보내주는 것이다. 그러면 쿠키를 내가 직접 만들지 않았지만, <b>JSESSIONID</b>라는 것이 들어있게 된다.

그래서 이러한 공간을 할당해놓고 썼었다.

여기까지가 우리가 진행해본 백엔드였다.

<br>

그런데 뭔가 요청이나 처리를 할 때, 너무 Server에 다 몰려 있다는 생각이 든다. Servlet이라고 하는 것의 업무가 너무 많은 것이다.

서블릿을 하나만 쓰겠다고 하고 서블릿에다가 코드를 다 붙이니까 분업을 하기 쉽지 않은 것이다. 그래서 업무를 쪼개기로 하였다.

User Controller, Board Controller 의 방식으로 User와 관련되어 있는 컨트롤러, Board와 관련되어 있는 컨트롤러를 쪼개는 것이다. 이런 식으로 기능별로 컨트롤러를 쪼개고, 그것에 따라 매핑할 수 있는 주소 같은 것들이 다 달라지는 것이다.

그리고 CRUD 작업들을 진행하기에 앞서서, 직접 내부적으로 메서드를 작성하지 않을 것이다. 대신 따로 <u>비즈니스 로직</u>을 처리하는 곳, <u>DB 관련 로직</u>을 처리하는 곳의 두 가지를 합쳐서 Manager의 역할을 하는 곳을 둘 것이다. 비즈니스 로직을 처리하는 곳은 <b>Service</b>이고, DB 관련 로직을 처리하는 곳은 Repository로 <b>DAO</b>라고 부른다.

이 경우에는 UserService, BoardService 그리고 UserDao, BoardDao 이렇게 두는 것이다.

그래서 어떤 요청이 들어왔을 때, '이번에는 User Controller로 처리를 할지, Board Controller로 처리를 할지' 결정을 해줘야 하는 것이다. 얼핏 보면 이 방식은 Front Controller 패턴이 아닌 것 같아 보인다. User Controller에 주소가 써 있고, Board Controller에도 주소가 써 있기 때문이다.

그래서 Spring에서는 맨 앞에 수문장을 하나 둔다. 이것이 바로 <b>Dispatcher Servlet</b> 이다. Dispatcher Servlet이 클라이언트와 서버 사이에서 버티고 있다가, 분석을 하는 것이다. 가령 '이 요청은 User Controller에게 주면 되겠다' 하면 User Controller에게 던진다. 반대로 '이 요청 Board Controller에게 주면 되겠다' 하면 Board Controller에게 던진다.

그래서 <u>Spring MVC에서 서블릿은 딱 한 개이다. 이 한 개가 바로 Dispatcher Servlet</u> 이다.

<br>

Controller, Service, Repository 와 같은 것들은 전부 Spring을 쓰니까 '빈 등록'을 해야 한다. 물론 직접 등록할 수도 있기는 하지만, 예전에 @Component라는 어노테이션을 배울 때 @Controller, @Service, @Repository 라는 어노테이션도 있다는 것을 배웠다. 이 어노테이션은 결국 내부적으로 @Component인데, 조금 더 명확하게 구분하고 (살짝 부가 기능이 있기는 하다) @Component 대신에 박는 어노테이션인 것이다.

이러한 것들을 package-scan만 쭉 해버리면 알아서 빈으로 등록해서 스프링 컨테이너가 다 관리를 해주는 것이다.

User Controller라는 것은 User Service를 가지고 있어야 한다. 그래야 컨트롤러에서 어떤 요청이 왔을 때 '이거 Service로 던져야겠다' 할 수 있는 것이다. Board Controller는 Board Service를 가지고 있어야 한다. 그러면 <b>User Controller가 User Service라는 객체가 필요하다는 점에서 DI(Dependency Injection) 개념이 필요해진다.</b> User Controller가 User Service를 의존하고 있기 때문에 의존성 주입을 해줘야 하는 것이다.

그래서  ```@Autowired```를 붙여 넣으면 스프링이 알아서 User Service를 User Controller에 톡 넣어주고, Board Service를 알아서 Board Controller에 톡 넣어준다. 우리가 다이렉트로 컨트롤러에서 리포지토리로 갈 일은 없다. <b>무조건 Repository로 가는 것은 Service를 통해서 호출하게 되어 있다.</b> 그래서 User Service도 내부적으로 User Repository를 가지고 있고, Board Service도 내부적으로 Board Repository를 가지고 있어야 한다. 이 또한 의존성 주입을 IoC Container가 알아서 해준다.


<br>

## MVC 패턴

MVC 패턴은 디자인 패턴 중 하나이다.