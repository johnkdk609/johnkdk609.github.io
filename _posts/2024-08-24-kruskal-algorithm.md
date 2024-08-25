---
layout: post
title: 크루스칼 알고리즘 (kruskal's algorithm)
categories: Algorithm
description: 나동빈, 2021, [이것이 취업을 위한 코딩 테스트다 with 파이썬], 한빛미디어
date: 2024-08-24 18:09:00 +0900
---
크루스칼 알고리즘(kruskal's algorithm)은 최소 신장 트리를 찾는 알고리즘이다. 우선 신장 트리가 무엇인지부터 알아보자.

## 신장 트리 (Spanning Tree)

신장 트리는 그래프 알고리즘의 한 유형으로, 하나의 그래프가 있을 때 <u>모든 노드를 포함</u>하면서 <u>사이클이 존재하지 않는</u> 부분 그래프를 의미한다. 이때 모든 노드가 포함되어 서로 연결되면서 사이클이 존재하지 않는다는 조건은 트리의 성립 조건이기도 하다.

가령 다음과 같은 그래프가 있다고 해보자.

<img src="https://github.com/user-attachments/assets/6370752d-c245-4c53-9091-0613c8e7b2ed" width="280px" />

그러면 가능한 신장 트리 예시는 다음과 같다.

<img src="https://github.com/user-attachments/assets/2ac8c805-4527-495e-bd92-437e75a6f3f5" width="280px">

그리고 신장 트리가 아닌 부분 그래프 예시는 다음과 같다.

<img src="https://github.com/user-attachments/assets/184ed6ff-cc3b-4f91-a9eb-9b2d93c1e020" width="280px">

위 예시의 경우, 그래프가 '노드 1'을 포함하고 있지 않기 때문에 신장 트리에 해당하지 않는다.

또 다른 신장 트리가 아닌 부분 그래프 예시는 다음과 같다.

<img src="https://github.com/user-attachments/assets/0eb365f9-e47f-45b9-9095-bd9c251732c1" width="280px">

위 예시의 경우 사이클이 존재하므로 신장 트리가 아니다.

<br>

## 크루스칼 알고리즘

크루스칼 알고리즘은 최소 신장 트리(Minimum Spanning Tree)를 찾는 알고리즘이다. 어떤 그래프의 각 간선에 가중치(weight)가 있을 때, 신장 트리 중에서 최소 비용으로 만들 수 있는 신장 트리가 '최소 신장 트리'이다.

가령 다음과 같이 가중치가 있는 그래프가 있을 때를 보자.

<img src="https://github.com/user-attachments/assets/0a197acd-f5bf-457c-ad9d-a76467fb97ca" width="290px">

위 그래프에서 최소 신장 트리는 다음과 같다.

<img src="https://github.com/user-attachments/assets/d02fb42d-7d1e-45ec-b9b1-509c43201c4e" width="290px">

<br>

크루스칼 알고리즘은 그리디(greedy) 알고리즘으로 분류된다. 먼저 모든 간선에 대해 정렬을 수행한 후, 가장 거리가 짧은 간선부터 집합에 포함시키는 것이다. 이때 사이클(cycle)을 발생시킬 수 있는 간선의 경우, 집합에 포함시키지 않는다.

크루스칼 알고리즘의 로직은 다음과 같다.

<b>1번. 간선 데이터를 비용에 따라 오름차순으로 정렬한다.
2번. 간선을 하나씩 확인하며 현재의 간선이 사이클을 발생시키는지 확인한다.
    Ⅰ. 사이클이 발생하지 않는 경우 최소 신장 트리에 포함시킨다.
    Ⅱ. 사이클이 발생하는 경우 최소 신장 트리에 포함시키지 않는다.
3번. 모든 간선에 대하여 2번의 과정을 반복한다.</b>

<br>

