---
layout: post
title: 크루스칼(kruskal) 알고리즘의 이해
categories: Algorithm
date: 2023-03-29 22:02:00 +09:00
description: FastCampus 개발자 취업 합격 패스 Chapter 20. 최소 신장 트리의 이해 1
---
크루스칼 알고리즘(Kruskal Algorithm)은 최소 신장 트리 알고리즘이다.

신장 트리(Spanning Tree)란, 원래 그래프의 모든 노드가 연결되어 있으면서 트리의 속성을 만족하는 그래프이다.

신장 트리의 조건은 다음과 같다.
* 본래의 그래프의 모든 노드를 포함해야 함
* 모든 노드가 서로 연결
* 트리의 속성을 만족시킴 (사이클이 존재하지 않음)

최소 신장 트리(Minimum Spanning Tree, MST)는 가능한 Spanning Tree 중에서, 간선의 가중치 합이 최소인 Spanning Tree를 지칭한다. 최소 신장 트리 알고리즘은 그래프에서 최소 신장 트리를 찾을 수 있는 알고리즘이다. 대표적인 최소 신장 트리 알고리즘으로는 **Kruskal's algorithm**, **Prim's algorithm**이 있다.


### 크루스칼 알고리즘 (Kruskal's algorithm)

크루스칼 알고리즘은 탐욕(greedy) 알고리즘을 기초로 한다. 당장 눈 앞의 최소 비용을 선택해서, 결과적으로 최적의 솔루션을 찾는 것이기 때문이다.

크루스칼 알고리즘의 로직은 다음과 같다.
1. 모든 정점을 독립적인 집합으로 만든다.
2. 모든 간선을 비용을 기준으로 정렬하고, 비용이 작은 간선부터 양 끝의 두 정점을 비교한다.
3. 두 정점의 최상위 정점을 확인하고, 서로 다를 경우 두 정점을 연결한다. (최소 신장 트리는 사이클이 없으므로, 사이클이 생기지 않도록 하는 것)


#### Union-Find 알고리즘

Union-Find 알고리즘이란 Disjoint Set을 표현할 때 사용하는 알고리즘이다. 트리 구조를 활용한다. 간단히 말하면, 노드들 중에 연결된 노드를 찾거나, 노드들을 서로 연결할 때(합칠 때) 사용한다.

Disjoint Set이란,
- 서로 중복되지 않는 부분 집합들로 나눠진 원소들에 대한 정보를 저장하고 조작하는 자료구조
- 공통 원소가 없는 (서로소) 상호 배타적인 부분 집합들로 나눠진 원소들에 대한 자료구조를 의미함

즉, Disjoint Set == 서로소 집합 자료구조

Union-Find 알고리즘에서는 고려할 점이 있는데, Union 순서에 따라 최악의 경우 링크드 리스트와 같은 형태가 될 수 있다. 트리의 깊이가 너무 깊어지는 것이고, O(N)의 시간이 걸리게 되어 시간 효율성을 낮출 수 있다.

이 문제를 해결하기 위해 **union-by-rank**, **path compression** 기법을 사용한다.


#### union-by-rank 기법

각 트리에 대해 높이(rank)를 기억해 두고, Union 시 두 트리의 높이(rank)가 다르면, 높이가 작은 트리를 높이가 큰 트리에 붙인다. 즉, 높이가 큰 트리의 루트 노드가 합친 집합의 루트 노드가 되게 하는 것이다.
만약 높이가 h - 1 인 두 개의 트리를 합칠 때는 한 쪽의 트리 높이를 1 증가시켜주고, 다른 쪽의 트리를 해당 트리에 붙여준다.

정리하자면 초기화를 하고 모든 원소는 높이(rank)가 0인 개별 집합인 상태에서, 하나씩 원소를 합칠 때 union-by-rank 기법을 사용한다면,
- 높이가 h인 트리가 만들어지려면, 높이가 h - 1 인 두 개의 트리가 합쳐져야 함
- 높이가 h - 1 인 트리를 만들기 위해 최소 n개의 원소가 필요하다면, 높이가 h인 트리가 만들어지기 위해서는 최소 2n개의 원소가 필요함
- 따라서 union-by-rank 기법을 사용하면, union/find 연산의 시간 복잡도는 O(N)이 아닌, O(log N)으로 낮출 수 있다.


#### path compression

path compression이란 find를 실행한 노드에서 거쳐간 노드를 루트에 다이렉트로 연결하는 기법이다. Find를 실행한 노드는 이후부터는 루트 노드를 한 번에 알 수 있게 된다.


### 크루스칼 알고리즘 코드 구현

<img width="329" alt="https://www.fun-coding.org/00_Images/kruscal_internal2.png" src="https://user-images.githubusercontent.com/88493727/228552950-6f31ab4b-0d49-4cc2-8b45-16e5e8462be9.png">

가령, 이러한 그래프가 있고, 이 그래프에서 도출된 최소 신장 트리가 이것이라고 한다면,

```python
mygraph = {
    'vertices': ['A', 'B', 'C', 'D', 'E', 'F', 'G'],    # 정점 정보
    'edges': [
        (7, 'A', 'B'),
        (5, 'A', 'D'),
        (7, 'B', 'A'),
        (8, 'B', 'C'),
        (9, 'B', 'D'),
        (7, 'B', 'E'),
        (8, 'C', 'B'),
        (5, 'C', 'E'),
        (5, 'D', 'A'),
        (9, 'D', 'B'),
        (7, 'D', 'E'),
        (6, 'D', 'F'),
        (7, 'E', 'B'),
        (5, 'E', 'C'),
        (7, 'E', 'D'),
        (8, 'E', 'F'),
        (9, 'E', 'G'),
        (6, 'F', 'D'),
        (8, 'F', 'E'),
        (11, 'F', 'G'),
        (9, 'G', 'E'),
        (11, 'G', 'F')
    ]
}

parent = dict()
rank = dict()

def find(node):
    # path compression 기법
    if parent[node] != node:    # 만약 node가 루트 노드가 아니면
        parent[node] = find(parent[node])   # node를 루트 노드에 바로 연결
    return parent[node]

def union(node_v, node_u):
    root1 = find(node_v)    # root1은 node_v의 루트 노드
    root2 = find(node_u)    # root2는 node_u의 루트 노드

    # union-by-rank 기법
    if rank[root1] > rank[root2]:
        parent[root2] = root1
    else:
        parent[root1] = root2
        if rank[root1] == rank[root2]:  # 두 루트 노드의 랭크가 같을 때
            rank[root2] += 1    # 루트가 되기로 한 것의 랭크를 하나 올린다.

def make_set(node): # 초기화할 때 필요한 메서드
    parent[node] = node
    rank[node] = 0

def kruskal(graph):
    mst = list()    # minimum spanning tree를 리스트로 생성

    # 1. 초기화
    for node in graph['vertices']:
        make_set(node)

    # 2. 간선 weight 기반 sorting
    edges = graph['edges']
    edges.sort()

    # 3. 간선 연결 (사이클이 없는)
    for edge in edges:
        weight, node_v, node_u = edge
        if find(node_v) != find(node_u):
            union(node_v, node_u)
            mst.append(edge)

    return mst
```

이렇게 된다. 이것을 실행해보면,

```python
print(kruskal(mygraph))
```

<img width="723" alt="image" src="https://user-images.githubusercontent.com/88493727/228559337-bad743cf-8466-4fca-a88c-720913d1dbdd.png">

이렇게 잘 출력된다. 최소 신장 트리가 만들어진 것이다.
