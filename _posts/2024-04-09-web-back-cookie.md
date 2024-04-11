---
layout: post
title: Cookie
categories: Spring-SpringBoot
date: 2024-04-09 16:17:00 +0900
---
Cookie에 대해서 알아보겠다.

Cookie는 언제 사용하는 것일까? 가령 내가 쿠팡 사이트에 들어가서 삼성 갤럭시 폴드를 하나 장바구니에 담았다고 하자.

나는 지금 쿠팡 사이트에 로그인을 하지 않았다. 그런데 장바구니에 내가 담은 물건이 들어 있다. 로그인을 하지 않았는데 쿠팡 서버에서는 도대체 나의 어떤 것을 보고 내가 지금 장바구니에 이러한 물건을 저장했는지를 알 수 있을까?

<br>

내가 장바구니에 물건을 담는 순간, 쿠팡 서버에서 쿠키(Cookie)를 하나 만든다. 이 쿠키 안에는 물건이 담겨있다는 정보가 담겨 있고, 서버는 이 쿠키를 나에게 던져준다. 그리고 나는 이 쿠키를 받아서 가지고 있다.

이제 내가 "장바구니 보고 싶어" 하고 ```/cartView``` 요청을 날리면 "지금 나 이런 거 가지고 있으니까, 같이 넘겨서 이런 정보도 같이 보여줘"가 된다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4fc2eebb-36bd-462d-85b6-9bfcaa259163" width="600px" />

쿠키는 개발자 도구창을 열면 볼 수 있다. <kbd>F12</kbd>를 누르고 개발자 도구 창을 켠다. 그리고 Application 탭에 들어가서 Cookies를 클릭하고, ```https://cart.coupang.com```을 클릭하면 연결되어 있는 무수히 많은 쿠키들이 쭉 보인다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/62fcf020-e7df-4c97-8bca-d92a2669fcaa" width="1000px" />

이 중에서 음영 처리되어 있는 'sid'라는 쿠키가 내가 장바구니에 넣은 갤럭시 폴드에 대한 쿠키이다. 이제 이 쿠키를 삭제하고 새로고침하면 다음과 같이 된다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/a9bdd884-85b9-4ec5-8781-dd1ad4441ead" width="1000px" />

장바구니에 물건이 없어진 것을 볼 수 있다.

아까 갤럭시 폴드를 넣었던 쿠키를 지워버렸기 때문에 더 이상 이 쿠키를 쿠팡 사이트에 전송한다 한들, 서버에서는 "빈 장바구니 줄게" 가 되는 것이다.

매번 데이터베이스에 우리가 "어떤 사람이 장바구니에 무엇을 넣어놨어요" 하고 저장을 하면 데이터베이스에서도 그 정보를 확보하고 있으니 이는 자원 낭비이다. 이런 간단한 정보들 같은 경우 쿠키로 처리한다.

<br>

다시 장바구니에 담아보겠다. 따로 로그인을 하지 않은 상황이다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/3eb29a97-d9e0-490e-8d39-011039c0907f" width="1000px" />

그러면 다시 위 그림과 같이 쿠키가 생성된다.

이제 시크릿 탭을 켜보겠다. 시크릿 탭을 켜는 것은 새로운 브라우저를 켠 것이다. 크롬에서 일반 탭과 시크릿 탭은 서로 다른 브라우저로 인식을 한다.

이번에는 시크릿 탭에서 쿠팡을 검색하고 들어가 장바구니를 보자.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/fa420213-cee9-4e75-94fe-bd538445777c" width="1000px" />

장바구니에 없다. 즉, <b>쿠키라는 것은 브라우저 별로 관리를 하는 것</b>이다. <u>각 브라우저 별로 다른 클라이언트로 생각을 하겠다는 것이다.</u>

<br>

## Cookie

Cookie라는 것은 웹 서버가 클라이언트의 웹 브라우저에 저장하는 작은 데이터 조각이다.

필요에 따라 요청(request) 시 서버로 같이 전송된다. 무조건적으로 내가 가지고 있는 모든 쿠키들이 어떤 요청을 보낼 때마다 한 방에 다 가는 것이 아니라, 필요 시에 이러이러한 쿠키들을 보내겠다는 것이다.

쿠키는 Key : Value 형태의 문자열 데이터이다.

그리고 웹 브라우저(클라이언트) 별로 별도의 쿠키를 생성한다. 브라우저는 하나의 컴퓨터이다. 브라우저가 다르다면 다른 사용자인 것이다.

(공식 문서: <a href="https://jakarta.ee/specifications/platform/8/apidocs/javax/servlet/http/cookie">https://jakarta.ee/specifications/platform/8/apidocs/javax/servlet/http/cookie</a>)

<br>

## Cookie 사용 목적

Cookie의 사용 목적은 다음과 같다.

* 세션 관리 (사용자 아이디, 장바구니 등)를 위해 사용
* 사용자가 설정한 환경 등을 기억하여 페이지 제공
* 사용자의 행동과 패턴을 분석
* 사용자의 관심에 따른 광고를 타겟팅 하기 위해서 사용
