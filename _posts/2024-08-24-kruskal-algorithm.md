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

