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

그런데 이제는 서버와 서버 간 통신을 할 때에도 HTTP를 사용하는 것이다.