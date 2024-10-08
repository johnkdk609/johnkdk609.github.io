---
layout: post
title: 최단 경로(Shortest Path)를 반환하는 BFS
categories: Algorithm
date: 2024-10-08 13:35:00 +0900
---
BFS 알고리즘 문제를 풀 때에는 문제에서 '최단 거리(shortest distance)'를 요구하는 경우가 많다. 그런데 어떤 경우에는 '최단 경로(shortest path)'를 요구하기도 한다.

최단 경로를 고르기 위해서는 대체로 문제에서 방향 우선순위를 조건으로 제시해 준다. 왜냐하면 상, 하, 좌, 우 우선순위가 없다면 최단 경로는 여러 가지가 될 수 있기 때문이다.