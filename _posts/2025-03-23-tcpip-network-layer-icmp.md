---
layout: post
title: TCP/IP 4계층 &#35;5. 인터넷 계층(network)과 ICMP
categories: Network
description: CS 지식의 정석 - 디자인패턴 네트워크 운영체제 데이터베이스 자료구조
date: 2025-03-23 23:50:00 +0900
---
인터넷 계층(network layer)은 IP, ICMP, ARP 가 대표적이며 한 노드에서 다른 노드로 전송 계층에서 받은 세그먼트 또는 데이터그램을 패킷화 하여 전송한다.

IP, ARP는 중요하기 때문에 나중에 다루기로 하고, 이번에는 ICMP에 대해서 집중해보겠다.

## ICMP

<b>ICMP(Internet Control Message Protocol)는 노드와 노드 사이에서 통신이 잘 되나를 확인할 때 쓰는 프로토콜</b>이다.

이는 <u>데이터를 교환하는 데 사용되지 않는 프로토콜</u>이다. 즉 ICMP로 데이터를 교환하는 것은 불가능하고, 그냥 확인만 할 때 쓰는 프로토콜인 것이다.

<img src="https://github.com/user-attachments/assets/86704ac1-641b-4e9c-9470-3916baaa32aa" width="400px" />

위 그림과 같이 echo 를 하는 것이다. 메아리를 하듯이 "야~ 야~ 야~" 하고 "들려~ 들려~ 들려~" 하는 것이다.

일반적으로 테스팅에 사용된다.

IP와는 달리 TCP 또는 UDP 와 같은 전송 계층 프로토콜과 연관되지 않고 독립적인 비연결형 프로토콜이다.

```ping www.google.com``` 을 터미널에 입력하면 다음과 같이 응답이 온다.

<img src="https://github.com/user-attachments/assets/75bb5f87-f9d3-4aac-99db-9102e58952f8" width="500px" />

이것이 바로 ICMP 프로토콜을 기반으로 테스팅을 하는 것이다.