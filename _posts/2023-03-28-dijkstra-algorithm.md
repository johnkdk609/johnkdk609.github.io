---
layout: post
title: 다익스트라(Dijkstra) 알고리즘의 특징 및 
categories: Algorithm
description: FastCampus 개발자 취업 합격 패스 Chapter 20. 최단 경로 알고리즘의 이해
date: 2023-03-28 21:15:00 +09:00
---
최단 경로 문제란 두 노드를 잇는 가장 짧은 경로를 찾는 문제이다. 가중치 그래프(Weighted Graph)에서 간선(Edge)의 가중치 합이 최소가 되도록 하는 경로를 찾는 것이 목적이다.

다익스트라 알고리즘은 최단 경로 문제 종류 중에서, 단일 출발(single-source shortest path problem) 최단 경로 문제에 해당한다. 그래프 내의 특정 노드와 그래프 내 다른 모든 노드 각각의 가장 짧은 경로를 찾는 문제인 것이다.

단일 출발 최단 경로 문제 외에도 두 가지가 더 있다.
- 단일 출발 및 단일 도착 (single-source and single-destination shortest path problem)
- 전체 쌍 (all pair)

아무튼 다익스트라 알고리즘은 하나의 정점에서 다른 모든 정점 간의 각각 **가장 짧은 거리**를 구하는 문제이다.


### 다익스트라 알고리즘 특징

* 첫 정접을 기준으로 연결되어 있는 정점들을 추가해 가며, 최단 거리 갱신
* 다익스트라 알고리즘은 너비우선탐색(BFS)와 유사
* 우선순위 큐를 활용. MinHeap 방식을 사용해서 현재 가장 짧은 거리를 가진 노드 정보를 먼저 꺼냄


### 다익스트라 알고리즘 로직

1. 첫 정점을 기준으로 배열을 선언하여 첫 정점에서 각 정점까지의 거리를 저장
2. 우선순위 큐에서 노드를 꺼냄
3. 2번의 과정을 우선순위 큐에 꺼낼 노드가 없을 때까지 반복

이러한 다익스트라 알고리즘을 코드로 구현하면 다음과 같다.

가령, 다음과 같은 그래프가 있고 A에서 각 정점까지의 최단 경로를 구하는 문제라고 할 때,

![https://www.fun-coding.org/00_Images/dijkstra.png](https://www.fun-coding.org/00_Images/dijkstra.png "예제로 이해하는 다익스트라 알고리즘 (우선순위 큐 활용)")

```python
mygraph = {
    'A': {'B': 8, 'C': 1, 'D': 2},
    'B': {},
    'C': {'B': 5, 'D': 2},
    'D': {'E': 3, 'F': 5},
    'E': {'F': 1},
    'F': {'A': 5}
}

import heapq

def dijkstra(graph, start):

    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    queue = []
    heapq.heappush(queue, [distances[start], start])

    while queue:
        current_distance, current_node = heapq.heappop(queue)

        if distances[current_node] < current_distance:
            continue

        for adjacent, weight in graph[current_node].items():
            distance = current_distance + weight

            if distance < distances[adjacent]:
                distances[adjacent] = distance
                heapq.heappush(queue, [distance, adjacent])

    return distances

print(dijkstra(mygraph, 'A'))
```

이렇게 된다. 시작 정점 'A'만 거리를 0으로 두고, 나머지 정점들까지의 거리는 float('inf')로 초기화 하고 시작했다. 여기서 'inf'란 INFINITY 즉 무한을 의미한다. 그리고 heapq 모듈을 import 하여 min-heap을 구현하고 있다.
처음에 시작 정점인 'A'의 distance와 'A'를 우선순위 큐에 heappush로 삽입하고, queue가 빌 때까지 while 문으로 반복문을 돌렸다. 반복문 안에서는 heappop을 하면서 우선순위에 있는 것을 하나씩 뺐다.
그리고 추출(pop)한 정점과 인접한 노드들 각각에 대해, 첫 정점에서 각 노드로 가는 거리와 현재 배열에 저장되어 있는 첫 정점에서 각 정점까지의 거리를 비교한다. 배열에 저장되어 있는 거리보다, 첫 정점에서 해당 노드로 가는 거리가 더 짧을 경우, 배열에 해당 노드의 거리를 업데이트 한다.
그리고 배열에 해당 노드의 거리가 업데이트된 경우, 우선순위 큐에 넣는다. 결과적으로 너비 우선 탐색(BFS) 방식과 유사하게, 첫 정점에 인접한 노드들을 순차적으로 방문하게 되는 것이다.


### 다익스트라 알고리즘의 시간 복잡도

다익스트라 알고리즘의 시간 복잡도는 **O(Elog E)**이다. E는 간선(edge)의 약자이다. n개의 노드를 가지는 heap에 데이터를 삽입 또는 삭제시, 최악의 경우 root 노드에서 leaf 노드까지 비교해야 하므로 h=log2n 에 가깝다. 거기에 각 노드마다 인접한 간선들을 모두 검사하는 과정에 O(E)의 시간이 걸린다. 결과적으로 O(Elog E)의 시간이 걸리는 것이다.
