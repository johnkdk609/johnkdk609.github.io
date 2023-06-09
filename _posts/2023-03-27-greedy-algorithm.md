---
layout: post
title: 그리디 알고리즘의 한계
categories: Algorithm
description: FastCampus 개발자 취업 합격 패스 Chapter. 19 탐욕 알고리즘의 이해
date: 2023-03-27 22:22:00 +09:00
---
탐욕 알고리즘(Greedy Algorithm)은 최적의 해에 가까운 값을 구하기 위해 사용된다.

여러 경우 중 하나를 결정해야 할 때마다, **매순간 최적이라고 생각되는 경우를 선택**하는 방식으로 진행해서 최종적인 값을 구하는 방식이다.

그렇기에 탐욕 알고리즘은 한계가 있다.

반드시 최적의 해를 구할 수 있는 것은 아닌 것이다. **탐욕 알고리즘은 근사치 추정에 활용되고, 최적의 해에 가까운 값을 구하는 방법** 중 하나이다.

![https://www.fun-coding.org/00_Images/greedy.png](https://www.fun-coding.org/00_Images/greedy.png "그리디 알고리즘 예시 트리")

가령 위 그림에서 시작 노드에서 시작해 가장 작은 값을 찾아 leaf node 까지 가는 경로를 찾을 경우에

탐욕 알고리즘을 적용할 경우 시작 -> 7 -> 12를 선택하게 되므로 7 + 12 = 19가 된다.

하지만 실제로 가장 작은 값은 시작 -> 10 -> 5이며, 10 + 5 = 15가 답이다.
