---
layout: post
title: 네트워크의 기초 - 유니캐스트, 멀티캐스트, 브로드캐스트
categories: Network
description: CS 지식의 정석 - 디자인패턴 네트워크 운영체제 데이터베이스 자료구조
date: 2025-03-13 10:02:00 +0900
---
유니캐스트, 멀티캐스트, 브로드캐스트에 대해 알아보겠다.

## 유니캐스트

<b>유니캐스트(Unicast)란 1:1 통신을 말한다.</b> 대표적으로 HTTP 통신이 있다. 가장 일반적인 네트워크 전송 형태이다.

<img src="https://github.com/user-attachments/assets/88469b90-f708-47a2-b5a3-ac50fe381a25" width="350px" />

<br>

만약 내가 네이버에 요청을 하게 되면, 이것은 HTTP 요청을 한 것이다. (정확하게는 HTTPS 요청을 한 것이기는 하다.) 이것이 일대일 즉 유니캐스트 요청이다.

<br>
<hr>

## 멀티캐스트

<b>멀티캐스트(Multicast)는 1:N 통신을 말한다.</b> N 이지만 <u>모든 노드들에게 데이터를 전달하지는 않고 특정 그룹에게만 데이터를 전달</u>한다.

<img src="https://github.com/user-attachments/assets/52ea29ae-4534-477a-b3f0-268815fa8aa5" width="350px" />

<br>

<img src="https://github.com/user-attachments/assets/d41499d5-a46a-4b26-ade2-769071ec683c" width="300px" />

위 그림과 같이 노드가 연결돼 있을 때, 초록색으로 그룹을 만들어 놓으면 이 그룹한테만 데이터를 전달하는 것이 멀티캐스트인 것이다.

<br>
<hr>

## 브로드캐스트

<b>브로드캐스트는 1:N 통신을 말한다.</b> 그룹이 아닌 <u>연결되어 있는 모든 노드에게</u> 데이터를 전달한다. 예로는 ARP 가 있다.

<img src="https://github.com/user-attachments/assets/6fdc8174-ba54-4147-9f97-62fe2fff4aa9" width="350px" />

<br>

멀티캐스트처럼 특정 그룹을 나눠서 그 그룹에게만 데이터를 전달하는 것이 아니고, 그냥 연결돼 있는 모든 노드에 데이터를 보내는 것이다.