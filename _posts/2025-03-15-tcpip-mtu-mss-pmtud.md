---
layout: post
title: TCP/IP 4계층 &#35;2. MTU와 MSS와 PMTUD
categories: Network
description: CS 지식의 정석 - 디자인패턴 네트워크 운영체제 데이터베이스 자료구조
date: 2025-03-15 23:58:00 +0900
---
TCP/IP 4계층 중 MTU, MSS, PMTUD에 대해 알아보겠다.

## MTU

앞서서 패킷이 쪼개져서 데이터를 송 ･ 수신한다고 했다. <b>패킷으로 쪼개질 때 MTU(Maximum Transmission Unit)를 기준으로 쪼개진다.</b> MTU는 네트워크 통신할 때 할 수 있는 가장 큰 PDU의 크기를 말한다. 

Ex&#41; 터널의 높이 제한을 생각하면 된다. 트럭이 높이 제한에 걸리면 통과가 안 된다. 네트워크 장치와 장치마다 설정된 MTU가 있다. 만약 MTU가 1,500으로 설정이 되어 있다. 내가 4,000짜리를 2,000짜리 두 개로 쪼개면, 이 2,000짜리는 1,500을 통과할 수 없다. 만약 통과하려면 1,500, 500으로 쪼개든가, 패킷 크기를 다시 조절해서 보내야 한다.

이때 통신을 하는 양쪽 끝은 두 장치의 MTU 만이 아니라 중간의 모든 라우터, 스위치, 서버를 고려해야 한다. 네트워크 경로 상에 있는 아무 장치나 MTU보다 패킷이 크면 그 패킷은 분할될 수도 있다.

네트워크는 여러 개의 장치로 연결되어 있다.

<img src="https://github.com/user-attachments/assets/c4704d34-fdf1-4f22-bf17-9b4d4ea4529b" width="400px" />

위 그림을 볼 때, 어떤 장치의 MTU는 1,500이고, 어떤 장치의 MTU는 1,400이다. 그러면 패킷을 보낼 때 1,400짜리를 기반으로 생각해야 하는 것이다.

<img src="https://github.com/user-attachments/assets/a1de25fb-e9eb-4945-ba1b-e8d62697c902" width="1000px" />

위 그림을 볼 때, 처음에 1,500bytes 였던 데이터 패킷이 Router C 를 거치기 위해 Router B 에서 100bytes 와 1,400bytes 로 쪼개졌다(Fragmenting packet).

<br>

그런데 패킷이 분할되지 않는 경우도 있다. 패킷을 분할할 수 없어 네트워크 경로 상에 있는 어떠한 라우터나 장치의 MTU를 초과할 때 분할해서 전달하는 것이 아니라 전달을 아예 하지 않을 수도 있다.

<b>IPv6 는 분할을 허용하지 않는다.</b>

<img src="https://github.com/user-attachments/assets/e101d488-5f1e-4b2f-857a-cd71f4755e0c" width="600px" />

위 그림은 IPv4 의 헤더이다. 보면 Flags 라는 필드가 있는데, 이 필드에 만약 bit 가 1 이 되면 "Don't Fragment" 플래그가 활성화 된다. 이때 분할은 불가능하다.

