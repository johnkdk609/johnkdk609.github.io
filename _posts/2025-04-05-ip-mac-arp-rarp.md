---
layout: post
title: IP주소, MAC주소, ARP, RARP
categories: Network
description: CS 지식의 정석 - 디자인패턴 네트워크 운영체제 데이터베이스 자료구조
date: 2025-04-05 23:38:00 +0900
---
이번에는 IP 주소, MAC 주소, ARP, RARP 에 대해 알아보겠다.

## IP 주소

IP 주소(Internet Protocol Address)는 <b>논리적 주소</b>이며 컴퓨터 네트워크에서 장치들이 서로를 인식하고 통신을 하기 위해서 사용하는 특수한 번호이며 IP를 기반으로 통신한다고도 하지만 사실상 그 밑에 물리적 주소인 MAC 주소를 통해 통신한다.

예를 들어 쿠팡에서 택배를 시킨다고 했을 때, 판매자가 내 주소를 알아야 쿠팡을 통해서 그 택배를 보낼 수 있다.

<img src="/assets/img/captures/ip_address_coupang_ex.jpg" width="600px" />

컴퓨터끼리 데이터를 받는다고 했을 때에는 IP 주소라는 주소가 필요한 것이다. 이 주소는 논리적이라는 특징을 가진다. <u>논리적이란 '변한다'는 것</u>이다.

<br>

Starbucks에 가서 노트북을 스타벅스의 Wifi에 연결한다고 해보자. 그러면 Starbucks에서 주는 IP 주소가 할당이 된다.

다음 날에는 아이오아라는 개인 카페에 들어갔다고 해보자. 같은 노트북을 아이오아 카페의 Wifi에 연결하면 아이오아 카페에서 주는 IP 주소가 할당이 되는 것이다.

<img src="/assets/img/captures/ip-mac-starbucks-ex.jpg" width="600px" />

노트북은 동일한데 IP 주소는 다르게 할당되는 것이다. 어디로 가서 Wifi를 연결하느냐에 따라 달라지는 것이고, 이는 즉 '변한다', '논리적인 것이다'라고 할 수 있는 것이다.

그런데 이때 노트북은 동일하다. 사실 IP 주소 밑에 MAC 주소라는 게 있다. MAC 주소는 장치들의 네트워크 인터페이스에 부착된 번호인데, 보통 NIC라고 하는 네트워크 인터페이스 카드(LAN 카드)에 설정된 MAC 주소라는 것을 기반으로 통신하는 것이다. 데이터가 통신한다고 했을 때 'IP를 기반으로' 데이터를 통신한다고 했었다. 그런데 사실은 IP 밑에 MAC 주소가 있는 것이다. 이 장치와 통신을 해야 하기 때문에, 이 장치를 나타내는 것이 MAC 주소이다. 즉 이 장치 안의 네트워크 인터페이스에 설정된 번호가 MAC 주소이다.

<br>
<hr>

## MAC 주소

MAC 주소(Media Access Control Address)는 네트워크 인터페이스에 할당된 고유 식별자이며 보통 장치의 NIC에 할당된다.

<img src="/assets/img/captures/media-access-control-address.jpg" width="500px" />

(NIC는 Network Interface Card로, LAN 카드를 생각하면 된다.)

MAC 주소는 쉽게 테스팅할 수 있다. CMD를 켜고 ```ipconfig/add```을 입력하면 MAC 주소를 확인할 수 있다. 다음은 윈도우에서 한 예시 사진이다.

<img src="/assets/img/captures/ipconfig-all-testing.jpg" width="800px" />

여기서 04-42-1A 는 OUI 이고, OE-17-85 는 UAA 가 된다. 이런 식으로 각 장치마다 구별되는 번호를 할당한다.

이러한 MAC 주소는 네트워크 통신을 위해 존재하는 것이다. 그래서 이러한 것을 IEEE라는 네트워크 관련 단체가 관리한다.

IEEE(Institute of Electrical and Electronics Engineers)는 전기/전자/전산 분야의 국제 기구 및 학회이다. 관련 전문가들이 합병해서 창설한 국제조직이며, 관련 기술 공유와 표준 정의 등의 활동을 한다.

<img src="/assets/img/captures/ieee-logo.jpg" width="300px" />

<a href="https://regauth.standards.ieee.org/standards-ra-web/pub/view.html#registries">https://regauth.standards.ieee.org/standards-ra-web/pub/view.html#registries</a>

이것은 IEEE 사이트의 링크이다. 이 링크를 클릭하면 확인할 수 있다. 04-42-1A 를 입력하고 검색하면 다음과 같이 화면에 나타난다.

<img src="/assets/img/captures/ieee-search-ex.jpg" width="600px" />

ASUS 메인보드가 들어가 있는 본체를 사용한다고 했을 때 위와 같이 'Company Name: ASUSTek COMPUTER INC.' 라고 뜨는 것을 볼 수 있다. MAC 주소가 이런 식으로 IEEE가 할당한 OUI와 제조사에서 구별되는 코드인 UAA로 이루어져있음을 알 수 있다.

참고로 MAC주소는 보통은 유일하지만 유일하지 않을 수도 있다. 실수 또는 의도적으로 UAA를 중복되게 만들 수도 있다. 이때 동일 네트워크에서만 중복되지 않으면 문제 없긴 하다. 또한 NIC에 고정된 MAC주소를 변경할 수는 있으나 하지 않는 것을 권장하며 하는 것 자체를 어렵게 한 OS도 있다.

<img src="/assets/img/captures/pc-nic.jpg" width="500px" />

위 사진은 PC의 NIC 이다. 만약 핸드폰의 NIC이면 좀 작아진다.

<br>
<hr>

## ARP와 RARP

이 MAC주소는 ARP를 통해 파악이 가능하다.

ARP를 통해 논리적 주소인 IP주소를 물리적 주소인 MAC주소로 변환한다. 이와 반대로 RARP를 통해 물리적 주소인 MAC주소를 논리적 주소인 IP주소로 변환하기도 한다.

<img src="/assets/img/captures/arp-rarp.jpg" width="600px" />

IP주소는 ARP를 통해서 MAC주소로 변환이 되고, 반대로 MAC주소가 RARP를 통해서 IP주소로 변환된다. RARP 맨 앞의 R은 Reverse라고 생각하면 된다.

<br>

ARP의 과정은 다음과 같다.

1. <b>해당 IP주소에 맞는 MAC주소를 찾기 위해 해당 데이터를 '브로드캐스팅'을 통해 연결된 네트워크에 있는 장치한테 모두 보낸다.</b>
2. <b>맞는 장치가 있다면 해당 장치는 보낸 장치에게 유니캐스트로 데이터를 전달해 주소를 찾게 된다.</b>

다음 그림은 ARP의 주소를 찾는 과정이다.

<img src="/assets/img/captures/arp-broadcast-unicast.jpg" width="800px" />

A라는 컴퓨터가 '120.70.80.3'에 해당하는 주소가 뭔지 연결된 B, C, D에 모두 브로드캐스트로 보낸다. 그러면 이 중에 그러한 IP를 갖는 MAC주소를 가진 장치가 "어 난데?"라고 하면서 유니캐스트로 자신의 MAC주소를 반환하게 된다.

이런 방식으로 IP주소를 기반으로 MAC주소를 찾는 것이다. 그리고 이 반대 과정이 RARP 이다.

<img src="/assets/img/captures/arp-rarp-finding-mac-address.jpg" width="1100px" />

<br>

우리가 Wifi를 연결한다고 했을 때 IP를 부여받고, 그 IP 밑에 MAC주소가 있고, 그 MAC주소는 OUI, UAA로 이루어져 있고, 그리고 MAC주소를 찾는 과정은 ARP로 이루어져 있다.