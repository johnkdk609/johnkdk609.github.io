---
layout: post
title: 프림 알고리즘 (Prim's algorithm)의 이해
description: FastCampus 개발자 취업 합격 패스 Chapter 21. 최소 신장 트리의 이해 2
date: 2023-03-30 17:46:00 +09:00
categories: Algorithm
---
**프림 알고리즘(Prim's algorithm)**은 대표적인 최소 신장 트리 알고리즘이다.

시작 정점을 선택한 후, 정점에 인접한 간선 중 최소 간선으로 연결된 정점을 선택하고, 해당 정점에서 다시 최소 간선으로 연결된 정점을 선택하는 방식으로 최소 신장 트리를 확장해가는 방식이다.


#### Kruskal's algorithm & Prim's algorithm 비교
- 둘 다 탐욕 알고리즘(greedy algorithm)을 기반으로 하고 있다. (당장 눈 앞의 최소 비용을 선택해서, 결과적으로 최적의 솔루션을 찾는 것)
- Kruskal's algorithm은 가장 가중치가 작은 간선부터 선택하면서 MST를 구한다.
- Prim's algorithm은 특정 정점에서 시작, 해당 정점에 연결된 가장 가중치가 작은 간선을 선택, 간선으로 연결된 정점들에 연결된 간선 중에서 가장 가중치가 작은 간선을 택하는 방식으로 MST를 구한다.


#### 프림 알고리즘 로직

프림 알고리즘의 로직은 다음과 같다.
1. 임의의 정점을 선택, '연결된 노드 집합'에 삽입
2. 선택된 정점에 연결된 간선들을 간선 리스트에 삽입
3. 간선 리스트에서 최소 가중치를 가지는 간선부터 추출해서,
  * 해당 간선에 연결된 인접 정점이 '연결된 노드 집합'에 들어 있다면, 스킵함 (cycle 발생을 막기 위해서)
  * 해당 간선에 연결된 인접 정점이 '연결된 노드 집합'에 들어 있지 않다면, 해당 간선을 선택하고, 해당 간선 정보를 '최소 신장 트리'에 삽입
4. 추출한 간선은 간선 리스트에서 제거
5. 간선 리스트에 더 이상의 간선이 없을 때까지 3~4번을 반복


예시로 알아볼 그래프이다. 로직대로 이루어진 프림 알고리즘의 모습이다.

![프림 알고리즘 예제 사진 1](https://www.fun-coding.org/00_Images/prim1.png "https://www.fun-coding.org/00_Images/prim1.png")
![프림 알고리즘 예제 사진 2](https://www.fun-coding.org/00_Images/prim2.png "https://www.fun-coding.org/00_Images/prim2.png")
![프림 알고리즘 예제 사진 3](https://www.fun-coding.org/00_Images/prim3.png "https://www.fun-coding.org/00_Images/prim3.png")


#### 프림 알고리즘 (Prim's algorithm) 코드 작성

프림 알고리즘의 코드는 다음과 같다. 그래프는 위에 예시로 사용된 그래프 정보를 활용하였다.

```python
from collections import defaultdict
from heapq import *

# 그래프 데이터. 가중치, 먼저 연결된 노드, 뒤에 연결된 노드 순으로 튜플로 작성했다. 중복되지 않은 것만 표현했다.
myedges = [
    (7, 'A', 'B'), (5, 'A', 'D'),
    (8, 'B', 'C'), (9, 'B', 'D'), (7, 'B', 'E'),
    (5, 'C', 'E'),
    (7, 'D', 'E'), (6, 'D', 'F'),
    (8, 'E', 'F'), (9, 'E', 'G'),
    (11, 'F', 'G')
]

def prim(start_node, edges):    # 프림 알고리즘은 시작할 노드를 선택해야 하고, 전체 간선 정보를 담은 리스트를 넣어야 한다.
    mst = list()
    adjacent_edges = defaultdict(list)  # 각각의 노드에서 연결되어 있는 간선 정보를 관리해줄 필요가 있다. defalutdict로 해서 리스트로 리턴하도록 한 것.

    for weight, n1, n2 in edges:
        adjacent_edges[n1].append((weight, n1, n2)) # 아직 초기화를 하지 않았지만, defaultdict을 썼기 때문에 append를 바로 사용할 수 있다.
        adjacent_edges[n2].append((weight, n2, n1))

    connected_nodes = set(start_node)   # 집합 데이터 타입으로 선언. 맨 처음에 넣은 것이 start_node
    candidate_edge_list = adjacent_edges[start_node]
    heapify(candidate_edge_list)    # candidate_edge_list에서 가중치가 가장 작은 것을 pop하려면 이 데이터를 힙 구조로 만들어야 한다.

    while candidate_edge_list:
        weight, n1, n2 = heappop(candidate_edge_list)
        if n2 not in connected_nodes:   # 이미 있다면 사이클이 발생할 수 있기 때문에 배제
            connected_nodes.add(n2)
            mst.append((weight, n1, n2))    # 해당 간선은 최소 신장 트리(Minimum Spanning Tree)의 간선으로 선택이 된 것

            # 다시 while 구문으로 돌아가려면, 후보군을 더 넣어줘야 한다.
            for edge in adjacent_edges[n2]: # n2에서 추가된 이 노드에서 연결된 간선 리스트를 넣어줘야 하는 것
                if edge[2] not in connected_nodes:
                    heappush(candidate_edge_list, edge)

    return mst
```

위 코드를 보면,

1. 모든 간선 정보를 저장 (adjacent_edges)
2. 임의의 정점을 선택, '연결된 노드 집합(connected_nodes)'에 삽입
3. 선택된 정점에 연결된 간선들을 간선 리스트(candidate_edge_list)에 삽입
4. 간선 리스트(candidate_edge_list)에서 최소 가중치를 가지는 간선부터 추출
  * 해당 간선에 연결된 인접 정점이 '연결된 노드 집합'에 이미 들어 있다면, 스킵 (cycle 발생을 막기 위해)
  * 해당 간선에 연결된 인접 정점이 '연결된 노드 집합'에 들어 있지 않으면, 해당 간선을 선택하고, 해당 간선 정보를 '최소 신장 트리(mst)'에 삽입
    * 해당 간선에 연결된 인접 정점의 간선들 중, '연결된 노드 집합(connected_nodes)'에 없는 노드와 연결된 간선들만 간선 리스트(candidate_edge_list)에 삽입
      * '연결된 노드 집합(connected_nodes)'에 있는 노드와 연결된 간선들은 간선 리스트에 삽입해도 해당 간선은 스킵될 것이기 때문
      * 어차피 스킵된 간선을 간선 리스트(candidate_edge_list)에 넣지 않음으로 해서, 간선 리스트(candidate_edge_list)에서 최소 가중치를 가지는 간선부터 추출하기 위한 자료구조 유지를 위한 effort을 줄일 수 있다. (ex. 최소힙 구조 사용)
5. 선택된 간선은 간선 리스트에서 제거
6. 간선 리스트에 더 이상의 간선이 없을 때까지 3~4번 반복

의 구조로 구현된 것을 알 수 있다.

위 코드를 실행해보면,

```python
print(prim('A', myedges))
```

![image](https://user-images.githubusercontent.com/88493727/228792342-bfa3dc63-e959-424e-9a61-86fc3b73538f.png)

이렇게 잘 출력된다.


#### 시간 복잡도

프림 알고리즘의 시간 복잡도는 **O(Elog E)** 이다. E는 간선(Edge)이다. 최악의 경우, while 구문에서 모든 간선에 대해 반복하고, 최소 힙 구조를 사용하기 때문이다.
