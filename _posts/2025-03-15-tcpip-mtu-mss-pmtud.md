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

<img src="/assets/img/captures/c4704d34-fdf1-4f22-bf17-9b4d4ea4529b.png" width="400px" />

위 그림을 볼 때, 어떤 장치의 MTU는 1,500이고, 어떤 장치의 MTU는 1,400이다. 그러면 패킷을 보낼 때 1,400짜리를 기반으로 생각해야 하는 것이다.

<img src="/assets/img/captures/a1de25fb-e9eb-4945-ba1b-e8d62697c902.png" width="1000px" />

위 그림을 볼 때, 처음에 1,500bytes 였던 데이터 패킷이 Router C 를 거치기 위해 Router B 에서 100bytes 와 1,400bytes 로 쪼개졌다(Fragmenting packet).

<br>

그런데 패킷이 분할되지 않는 경우도 있다. 패킷을 분할할 수 없어 네트워크 경로 상에 있는 어떠한 라우터나 장치의 MTU를 초과할 때 분할해서 전달하는 것이 아니라 전달을 아예 하지 않을 수도 있다.

<b>IPv6 는 분할을 허용하지 않는다.</b>

<img src="/assets/img/captures/e101d488-5f1e-4b2f-857a-cd71f4755e0c.png" width="600px" />

위 그림은 IPv4 의 헤더이다. 보면 Flags 라는 필드가 있는데, 이 필드에 만약 bit 가 1 이 되면 "Don't Fragment" 플래그가 활성화 된다. 이때 분할은 불가능하다.

<br>
<hr>

## MTU와 MSS

MTU는 IP 헤더와 TCP 헤더의 크기까지 합치지만 <b>MSS(Maximum Segment Size)는 TCP에서 사용할 수 있는 데이터의 크기이자 TCP 헤더, IP 헤더를 뺀 크기</b>를 말한다.

<u>일반적으로 MTU는 1,500바이트이며 MSS는 1,460바이트이다.</u> 그렇기 때문에 MTU가 1,500이라도 데이터는 보통 1,460바이트 이하의 크기로 보내야 전달이 된다.

<img src="/assets/img/captures/365544c5-5545-4e22-b4ff-a5e243a4902f.png" width="700px" />

위 그림을 보면, MTU(1,500 byte)에서 IP Header(20 byte), TCP Header(20 byte)를 빼서 MSS가 1,460 byte 가 되는 것을 알 수 있다.

여기서 이더넷 헤더 14바이트, FCS 4바이트가 포함된 것을 통틀어 이더넷 프레임이라고 한다. <b>FCS(Frame Check Sequence)은 데이터의 에러 검출을 돕기 위해 삽입되는 필드</b>이다. (CRC에 의해 생성된 값이 여기에 포함)

따라서 이더넷 프레임의 크기는 일반적으로 1,518 바이트이다.

<br>
<hr>

## PMTUD

<b>PMTUD(Path MTU Discovery)는 수신자와 송신자의 경로 상에서 장치가 패킷을 누락한 경우 테스트 패킷의 크기를 낮추면서 MTU에 맞게끔 반복해서 보내는 과정</b>을 말한다.

앞서서 패킷이 분할되지 않는 경우가 발생할 수 있다. MTU를 초과할 때 분할해서 전달하는 것이 아니라 전달을 아예 하지 않을 수도 있는 것이다. 그러면 패킷을 줄여서 다시 보낸다. 이것을 바로 PMTUD라고 한다.