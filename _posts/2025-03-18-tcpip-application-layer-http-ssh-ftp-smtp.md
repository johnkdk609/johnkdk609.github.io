---
layout: post
title: TCP/IP 4계층 &#35;3. 애플리케이션 계층(application) HTTP, SSH, FTP, SMTP
categories: Network
description: CS 지식의 정석 - 디자인패턴 네트워크 운영체제 데이터베이스 자료구조
date: 2025-03-18 23:37:00 +0900
---
애플리케이션 계층 중 HTTP, SSH, FTP, SMTP 에 대해 알아보겠다. HTTP, SSH, FTP, SMTP는 대표적인 프로토콜이며, 애플리케이션 계층(Application Layer)은 웹 서비스, 이메일 등 서비스를 실질적으로 사람들에게 제공하는 층이다.

## HTTP

HTTP(Hypertext Transfer Protocol)은 처음에는 서버와 브라우저 간에 데이터를 주고 받기 위해 설계된 프로토콜이다. 지금은 브라우저 뿐만 아니라 서버와 서버간의 통신을 할 때에도 많이 이용한다.

<img src="https://github.com/user-attachments/assets/cb5724a4-7fe4-460f-b8a9-403ffecad53d" width="700px" />

위 그림을 보자. 예전에는 사용자가 접속해서 브라우저가 있고, 브라우저를 통해서 "www.naver.com"를 입력하여 네이버에게 요청을 했다. 그러면 네이버에서 해당 요청에 따른 응답 데이터를 보내는 것이다. 예전에는 이렇게 할 때에만 HTTP를 사용했다.

그런데 이제는 서버와 서버 간 통신을 할 때에도 HTTP를 사용한다. WebScraping을 예로 들어보자. 내가 타겟팅하는 서버한테 접속해서 데이터를 가져온다. 서버와 서버 간에 HTTP를 보내기도 한다.

또 MSA라는 서버와 서버 도메인 별로 나눠서 하는 분산 아키텍처가 있다. 이때 서버와 서버 간에 HTTP로 통신하기도 한다. 물론 gRPC라는 프로토콜이 많이 사용되긴 하지만, HTTP 기반으로 하기도 하는 것이다.

### 1. HTTP는 헤더를 통한 확장이 쉽다.

브라우저에서 네이버를 새로고침 하면 여러 가지 데이터를 받아온다.

<img src="https://github.com/user-attachments/assets/b3bdea91-71cb-40c9-947b-1c15bf34be96" width="1000px" />

여러 가지 데이터를 받아오는데, 그 중 하나를 클릭하고 헤더 정보를 본다. 보면 응답 헤더(Response Headers), 요청 헤더(Request Headers) 같은 것들이 있는데 요청할 때 어떤 쿠키값을 자동으로 보낸 것을 알 수 있다. 이것 뿐만 아니라 어떠한 정보를 바탕으로 네이버에 보낸다고 했을 때, 헤더에 어떤 값을 추가해서 보낼 수 있는 것이다. 그렇기 때문에 HTTP는 헤더를 통한 확장이 쉽다.

<img src="https://github.com/user-attachments/assets/396146f6-fc10-4b86-8cac-bee8f66d6721" width="600px" />

헤더 값에다가 어떠한 값을 넣어서 HTTP 요청을 할 때 쉽게 다른 값을 추가할 수 있다.

### 2. HTTP는 stateless 하다.

동일한 연결에서 연속적으로 수행되는 두 요청 사이에 연속적인 상태(state)값은 없다.

<img src="https://github.com/user-attachments/assets/c888962d-ec1a-46f4-9141-b99530892e2f" width="300px" />

위 그림을 보자. 어떠한 작업도 하지 않은 상태에서 내가 요청을 해서 데이터를 받았다(연보라색). 그 다음에 다시 요청을 하고 받는다고 했을 때(하늘색) 이 서버가 내가 홍철인지 알 수 있을까? 없다는 것이다. 상태를 저장하지 않는 stateless 하기 때문이다.

<img src="https://github.com/user-attachments/assets/3594c872-166a-46d8-94f3-0f6c04e6dcc6" width="500px" />

위 그림과 같이 클라이언트 측에서 "Hey Remember Me?"를 했을 때 서버 측에서 "Umm.......No!" 라는 반응이 나오는 것이다.

<br>
<hr>

## SSH

SSH(Secure SHell Protocol)는 보안되지 않은 네트워크에서 네트워크 서비스를 안전하게 운영하기 위한 암호화 네트워크 프로토콜이다.

