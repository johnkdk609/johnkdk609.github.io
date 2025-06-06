---
layout: post
title: 네트워크의 기초 &#35;2. 네트워크 토폴로지 &#58; 버스, 스타, 트리, 링, 메시
categories: Network
description: CS 지식의 정석 - 디자인패턴 네트워크 운영체제 데이터베이스 자료구조
date: 2025-03-12 15:34:00 +0900
---
네트워크 토폴로지 중 버스, 스타, 트리, 링, 메시 토폴로지에 대해 알아보겠다.

<b>네트워크 토폴로지(Network Topology)란 노드와 링크가 어떻게 구성되어 있는지를 뜻한다.</b>

## 버스 토폴로지

<img src="/assets/img/captures/f7156a6f-b254-4537-bc52-aa3b654874af.jpg" width="600px" />

버스 토폴로지는 위와 같은 형태를 보인다. 하나의 회선, 하나의 링크에 노드가 부착이 되어 있는 것이다.

특징
- 하나의 회선에 여러 개의 노드
- 노드 추가, 삭제 쉬움
- 설치 비용 적음

장점
- 소규모 네트워크를 구축하기 매우 쉬움
- 한 노드에 장애가 발생해도 다른 노드에 영향 X

단점
- 메인 링크에 많은 트래픽이 생기면 정체 현상 발생 가능성 높음 (패킷 손실률 높음)
- 메인 링크 망가지면 큰 문제

<br>
<hr>

## 스타 토폴로지

<img src="/assets/img/captures/ccab2ca9-a78a-41a8-a6b4-6ca030e70942.jpg" width="600px" />

<br>

특징
- 중앙에 있는 노드들 기반으로 연결된 형태
- 노드 추가, 삭제 쉬움

장점
- 중앙노드가 아닌 한 노드에 장애가 발생해도 다른 노드에 영향 X
- 안정성이 높음. 중앙노드가 아닌 한 노드에 침해가 발생했을 때 다른 노드로 확장하기가 어렵기 때문. 다른 노드로 가려면 중앙노드를 무조건 거쳐야 하고 보통의 <u>스타 토폴로지는 중앙노드의 방화벽 등을 더욱 깐깐하게 해놓음</u>.
- 한 링크에 문제가 생겨도 해당 부분만 영향을 받고 나머지 부분은 정상적으로 작동함.

단점
- 중앙노드 에러 시 큰 문제

<br>
<hr>

## 트리 토폴로지

<img src="/assets/img/captures/8f83ebb1-7d99-4648-abc2-ed30496d098e.jpg" width="700px" />

<br>

특징
- 트리 형태 (계층적 토폴로지라고도 함)
- 노드 추가, 삭제 보통 (리프노드를 기반으로 확장은 용이 but, 다른 노드는 어려움)
- 버스 토폴로지와 스타 토폴로지의 하이브리드 형태

장점
- 노드 확장이 용이 (주로 리프노드로 확장함)
- 리프노드의 에러는 나머지 부분에 영향을 미치지 않음

단점
- 특정 노드 트래픽 집중 시 하위노드에 영향
- 루트노드에 문제가 생기면 전체 네트워크에 큰 문제

<br>

위 왼쪽 그림을 보면 트리 토폴로지는 버스 토폴로지와 스타 토폴로지의 하이브리드 형태를 보인다. 세로로 하나의 커다란 링크가 있는데 이는 버스 토폴로지의 중앙 회선과 같은 역할을 하는 것이고(Backbone Cable), 그 옆에 가지로 나와 있는 것은 중앙(Hub)을 기준으로 노드들이 연결돼 있는 것이 마치 스타 토폴로지와 같은 형태를 하고 있는 것이다.

<b>백본케이블</b> : 여러 소형 네트워크들을 묶어 대규모 파이프라인을 통해 극도로 높은 대역폭으로 다른 네트워크들의 집합과 연결되는 네트워크 (대규모 패킷 통신망)

<br>
<hr>

## 링형 토폴로지

<img src="/assets/img/captures/5cdc9f81-3f0a-4d53-b745-a51ca9eaf0f8.jpg" width="700px" />

<br>

특징
- 고리 형태
- 노드 추가, 삭제가 쉬움

장점
- 노드 수가 많아져도 데이터 손실이 없음. 토큰을 기반으로 연속적으로 노드를 거치며 통신 권한 여부를 따지고 해당 권한이 없는 노드는 데이터를 전달 받지 않음.

단점
- 링크 또는 노드가 하나만 에러 발생해도 전체 네트워크에 영향
- 토큰이 없는 노드는 통신에 참여를 못하며 데이터 공유가 안 됨.

<br>

링형 토폴로지에서 노드 추가, 삭제는 원형 연결 리스트를 생각하면 이해하기 쉽다. 

위 오른쪽 그림을 보면 1번 노드에서 4번 노드로 데이터를 전달한다고 했을 때, 연속적으로 노드를 거치긴 거친다. 거치면서 토큰 권한이 있는지만 보는 것이다. 1번에서 2번으로 갔을 때, 2번 노드는 토큰 권한이 없다. 이제 2번에서 3번으로 가는데 3번도 토큰 권한이 없다. 이제 3번에서 4번으로 가는데 토큰 권한이 있다. 왜냐하면 1번에서 4번으로 보내기로 했기 때문이다.

그래서 노드 수가 많아져도 데이터 손실이 없다.

토큰은 장점이면서 단점이 될 수 있는데, 공유가 안 된다. 일대일 밖에 안 된다는 것이다.

<br>
<hr>

## 메시 토폴로지

<img src="/assets/img/captures/a779ccd5-0347-4733-827b-c850e76ea619.jpg" width="400px" />

<br>

특징
- 그물망 형태
- 노드 추가, 삭제 어려움
- 풀(full) 메시 토폴로지의 경우 n &#215; (n - 1) / 2 의 회선이 필요함.

장점
- 안정성이 높음. 한 노드가 장애가 나도 다른 노드에 영향을 미치지 않음
- 트래픽을 분산할 수 있음.

단점
- 회선이 비효율적으로 많기 때문에 구축 비용이 고가.

<br>

메시 토폴로지는 <b>full mesh topology</b>, <b>partially-connected mesh topology</b> 의 두 가지로 분류된다. 그런데 보통 메시 토폴로지라 하면 full mesh topology를 지칭한다.

메시 토폴로지에서 노드를 삭제하기 어려운 이유는, 한 노드를 삭제하면 그 노드에 연결된 회선을 모두 삭제해야 하는데 이 수가 많기 때문이다. <u>네트워크 설계를 할 때 회선에 비용이 많이 들기 때문에 무조건 노드를 삭제했을 때 연결된 회선은 삭제해야 한다.</u> 노드를 추가할 때에는 새로운 노드에 대해 다른 모든 노드들과 회선을 연결해야 하므로 비용이 매우 크다.

메시 토폴로지에서는 각 노드에 회선이 많이 연결돼 있기 때문에 한 노드에 장애가 나도 다른 노드에 영향을 미치지 않는다.

그리고 한 노드에 트래픽이 많이 모인다고 해도, 연결된 다른 회선들로 분산이 가능하다는 장점이 있다.