---
layout: post
title: TCP의 연결 해제 &#58; 4 Way Handshake 와 TIME_WAIT
categories: Network
description: CS 지식의 정석 - 디자인패턴 네트워크 운영체제 데이터베이스 자료구조
date: 2025-03-25 16:00:00 +0900
---
만남이 있으면 헤어짐이 있듯이 연결이 있으면 그 연결을 끊는 '연결 해제 과정'이 있다.

연결 해제는 '4 Way Handshake'라는 과정을 통해 이뤄진다.

<img src="https://github.com/user-attachments/assets/96927abf-ffee-4804-9141-f117a34d0bda" width="750px" />

일단 연결이 성립돼 있는 과정이므로 Client와 Server는 모두 ESTABLISHED 상태이다.

처음에 클라이언트가 FIN_WAIT_1 상태가 되면서 FIN 세그먼트를 보낸다. 서버는 FIN 세그먼트를 받고 CLOSE_WAIT 상태가 된다. (CLOSE를 기다린다는 것) 그리고 서버는 클라이언트에게 ACK를 보낸다. 클라이언트는 ACK를 받고 FIN_WAIT_2 상태가 된다. 그 다음에 서버는 조금 이따가 LAST_ACK라는 단계가 되면서 FIN 태그를 클라이언트에게 보낸다. 그러면 클라이언트는 <b>TIME_WAIT</b> 상태가 되면서 일정 시간 이후에 CLOSED 상태가 된다. TIME_WAIT가 되면서 ACK를 보내고 바로 CLOSED가 되는 것이 아닌, TIME_WAIT 만큼의 시간 이후에 CLOSED가 된다. 그리고 서버는 ACK를 받아 CLOSED가 된다.

이 4 단계를 정리하면 다음과 같다.

1. 먼저 클라이언트가 연결을 닫으려고 할 때 FIN으로 설정된 세그먼트를 보낸다. 그리고 클라이언트는 <b>FIN_WAIT_1</b> 상태로 들어가고 서버의 응답을 기다린다.
2. 서버는 클라이언트로 ACK라는 승인 세그먼트를 보내고 <b>CLOSE_WAIT</b> 상태에 들어간다. 클라이언트가 세그먼트를 받으면 <b>FIN_WAIT_2</b> 상태에 들어간다.
3. 서버는 <b>LAST_ACK</b> 상태가 되며 일정 시간 이후에 클라이언트에 FIN이라는 세그먼트를 보낸다.
4. 클라이언트는 <b>TIME_WAIT</b> 상태가 되고 다시 서버로 ACK를 보내서 서버는 <b>CLOSED</b> 상태가 되며 이후 클라이언트는 어느 정도의 시간(TIME_WAIT으로 설정된 시간)을 대기한 후 연결이 닫힌다.

위와 같이 네 개의 과정을 거쳐서 연결이 해제가 된다.

<br>
<hr>

## TIME_WAIT

여기서 TIME_WAIT 에 주목해야 한다. 클라이언트가 기다리지 않고 바로 CLOSED가 되면 빠르지 않을까 의문이 들 수 있다.

TIME_WAIT이 필요한 첫 번째 이유는 지연 패킷을 받기 위함이다. 서버로부터 FIN을 받고 나서, 어떤 패킷이 올 수 있다. 이렇게 하여 데이터의 무결성을 지킨다. 무결성이란 일관성, 정확성이라고 할 수 있다. 100의 데이터가 있다면 100을 받아야지 80만 받으면 안 되는 것이다. 즉, 전체적인 데이터를 받게끔 하는 것이 TIME_WAIT의 역할이다.

TIME_WAIT의 두 번째 필요성을 알아보자. 서버가 LAST_ACK가 된 상태에서 TIME_WAIT 다음에 서버가 CLOSED가 된다. 만약 클라이언트가 TIME_WAIT 없이 바로 CLOSED가 된다면, 클라이언트는 CLOSED가 됐는데 서버는 LAST_ACK 상태가 되어버린 것이다. 즉 클라이언트만 CLOSED가 된 것이다. 서버가 다시 연결을 하려면 둘 다 CLOSED된 상태에서 해야 한다. 즉, 연결이 올바르게 되었는지를 보장하기 위해서 TIME_WAIT가 필요한 것이다.

정리하면 다음과 같다.

<b>TIME_WAIT은 지연 패킷이 발생했을 때 데이터 무결성을 해결하기 위함이다.</b> 두 배의 최대 세그먼트 수명(MSL) 시간을 기다린다. 기본적으로 MSL은 2분이다. 소켓이 바로 소멸되지 않고 일정 시간 유지되는 상태를 말하며 지연 패킷 등의 문제점을 해결하는 데 쓰인다. <b>또한 연결을 올바르게 닫힌 상태로 만들기 위해 존재한다.</b> 예를 들어 CLOSED가 아닌 LAST_ACK로 되어 있으면 그 다음 연결 때 오류가 나타난다.

<br>

CentOS6, 우분투에는 60초로 설정되어 있으며 윈도우는 4분으로 설정되어 있다. 즉, OS마다 조금씩 다를 수 있다.

우분투 설정파일을 보면 다음과 같이 되어 있다.

<img src="https://github.com/user-attachments/assets/82253b5e-f82a-4cf9-89e1-d7018d719088" width="550px" />

만약 우분투를 사용한다면 이런 식으로 설정 파일을 통해서 TIME_WAIT가 어느 정도로 설정이 되어 있는가를 볼 수 있다.

<br>
<hr>

## 연결성립 + 연결해제 과정 정리

다음 그림은 상태를 다이어그램으로 나타낸 것이다.

<img src="https://github.com/user-attachments/assets/102ebab6-a1be-4c26-bde3-ae7f27059531" width="800px" />

처음에 서버가 CLOSED 상태였다가 LISTEN이 되면서 받게 된다. 그리고 이제 SYN / SYN + ACK 등의 과정을 거쳐서 연결이 성립된다.

그 다음에 연결이 해제될 때 FIN_WAIT_1 → CLOSE_WAIT → FIN_WAIT_2 → LAST_ACK 를 거쳐서 연결이 해제된다. TIME_WAIT 상태를 거쳐서 이후에 서버와 클라이언트 모두 다시 CLOSED가 된다.

그림 상단에 'Timeout after two maximum segment lifetimes (2*MSL)' 이라고 되어 있는데 이를 통해 TIME_WAIT는 MSL의 두 배 시간이라는 것을 알 수 있다.

<br>

이렇게 해서 TCP의 연결 해제 과정에 대해 알아봤다.