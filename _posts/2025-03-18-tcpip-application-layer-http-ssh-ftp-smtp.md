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

클라우드 서비스 배포를 할 때 AWS EC2를 사용하는 경우가 많다. 로컬 PC를 기반으로 다른 사용자들에게 서비스를 할 수도 있다. 그런데 이것은 사실상 힘든 일이다. AWS에 나의 코드를 놓고, 여기서 배포된 것을 기반으로 사용자들에게 서빙을 한다.

<img src="https://github.com/user-attachments/assets/af5b6d98-8bcd-4c46-b060-86b4d61364a4" width="450px" />

그런데 이때 나의 소중한 코드가 사용자들에게 노출이 되면 안 된다. 배포된 코드가 사용자에게 보여지는 인터페이스만 노출되어야지, 내부 코드가 노출이 되면 안 된다는 것이다. 그러면 어떻게 사용자들에게 보여지지 않고 안전하게 내 PC에서 AWS로 코드를 운반할 수 있을까? 이때 사용하는 것이 SSH 이다.

다음은 내 AWS 내의 EC2에 접근하는 모습이다. 보통 프라이빗 키가 있는 경로에서 이런식으로 키를 명시하고 실행한다.

```
ssh <pem> <user>@<serverIP>
```

pem 이라는 확장자를 가지는 프라이빗 키를 AWS 에게서 받는다. 이것을 기반으로 "AWS야, 이건 내 서버야. 난 xxx이고 내가 지금 접속하는 서버는 내 계정을 기반으로 만든 서버야" 라고 인증을 하는 것이다. 그래서 명령어가 위와 같은 것이다.

<img src="https://github.com/user-attachments/assets/fb300942-033d-4c9b-9e4d-2087bd8bd268" width="900px" />

그러면 위와 같은 검은 화면을 보게 될 것이다. 위와 같은 화면으로 접속해서 리눅스 명령어를 통해 CLI 환경에서 작업을 진행한다. 또한 SCP를 이용해서 SSH를 이용해 파일을 전송할 수 있다.

```scp <source> <destination>```

이런 식으로 해서 AWS에 안전하게 코드를 배포하고, 이를 기반으로 해서 사용자들에게 서빙을 할 수 있다.

<br>
<hr>

## FTP

FTP(File Transfer Protocol)는 노드와 노드간에 파일을 전송하는 데 사용되는 프로토콜이다. 보통 로컬 PC에서 원격 PC로 파일을 보낼 때 쓰는 것이다.

지금은 파일을 암호화해서 전송하는 FTPS 또는 SFTP로 대체되고 있다.

대표적인 FTP 소프트웨어로 FileZilla 같은 것이 있다.

<img src="https://github.com/user-attachments/assets/0d0ec4d8-8c5c-4143-8341-aa337d35654b" width="600px" />

FileZilla를 보면 다음과 같이 GUI 가 대표적이다.

<img src="https://github.com/user-attachments/assets/11971055-2d45-48ad-a030-7593ed785388" width="700px" />

직전에 봤던 AWS의 CLI 환경의 경우 명령어만 입력할 수 있었는데, 위 FileZilla의 경우 GUI를 사용해서 drag and drop 을 통해 파일을 보낼 수 있는 것이다.

<br>
<hr>

## SMTP

인터넷을 통해 메일을 보낼 때 사용되는 프로토콜(Simple Mail Transfer Protocol). 보통 서비스를 운영하면 메일링 서비스를 하게 되는데 node.js를 통해 메일을 보낸다면 이를 통해 보내야 한다.

어떤 사람에게 메일을 보내야 한다고 했을 때, 이때 HTTP를 사용하는 것이 아니다. 이때 SMTP라는 프로토콜을 기반으로 보내게 된다. 만약 내가 서비스를 운영한다고 했을 때 메일링 서비스가 필요하다. 예를 들어 어떤 것을 새롭게 출시하든, 에러가 발생했든 어떤 것을 공지할 게 필요하다고 했을 때 서비스의 사용자들에게 알림으로 알려줄 수도 있지만, 알림 + 메일로 알려주는 것이 가장 좋다.

서비스를 운영한다고 했을 때 메일링 서비스를 구축해야 한다. 위탁 업체를 통해서도 할 수 있지만, 스스로도 구현할 수 있다. 자바스크립트 진영에서는 Nodemailer 라는 라이브러리가 있는데 JS 기반으로 SMTP를 통해 메일을 보낼 수 있는 라이브러리이다.

<img src="https://github.com/user-attachments/assets/c882c17e-f5b3-49c6-b58f-da4b1cc8b0c3" width="350px" />

이런식으로 설명을 보면 SMTP를 통해서 보낸다라고 되어 있다.

<img src="https://github.com/user-attachments/assets/e822fa84-b0be-4332-952c-5d4d0edf8c79" width="450px" />

중요한 것은 SMTP란 인터넷을 통해 메일을 보낼 때 사용되는 프로토콜이라는 것이다.