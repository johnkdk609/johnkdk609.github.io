---
layout: post
title: 트리의 특성을 활용한 크루스칼 알고리즘 최적화
categories: Algorithm
date: 2024-11-15 23:43:00 +0900
---
크루스칼(Kruskal) 알고리즘은 MST(Minimum Spanning Tree, 최소 신장 트리)를 구현하기 위해 Union-Find 알고리즘을 활용하는 알고리즘이다. 최소 신장 트리를 구현하는 것이기 때문에 크루스칼 알고리즘에서는 트리의 특성을 사용해 최적화를 할 수 있다.

## 트리의 특성

트리의 중요한 특성은 바로 ```간선의 수 = 정점의 수 - 1``` 이라는 것이다. 만약 간선의 수가 정점의 수 이상이 된다면, 그것은 그래프에 사이클(cycle)이 발생했다는 것이고 더 이상 트리가 아니게 된다.

이러한 특성을 크루스칼 알고리즘에 사용하여, 불필요한 추가 탐색을 하지 않는다.

<br>

<a href="https://www.acmicpc.net/problem/1197" target="_blank">백준 1197번 최소 스패닝 트리</a> 문제를 이러한 조건을 대입해 풀어보겠다.

답안 코드는 다음과 같다.

```python
import sys
sys.setrecursionlimit(100000)   # 제한 풀어야 백준에서 통과함
input = sys.stdin.readline


def find_parent(x):
    if parent[x] != x:
        parent[x] = find_parent(parent[x])
    return parent[x]


def union_parent(x, y):
    xp = find_parent(x)
    yp = find_parent(y)
    if xp < yp:
        parent[yp] = xp
    else:
        parent[xp] = yp
    return


V, E = map(int, input().split())
parent = [a for a in range(V + 1)]

edges = []
total = 0

for _ in range(E):
    a, b, cost = map(int, input().split())
    edges.append((cost, a, b))

edges.sort()

edge_cnt = 0    # 최적화 => (트리의 간선의 수 == 정점의 수 - 1) 를 사용하기 위한 변수 edge_cnt

for edge in edges:
    cost, a, b = edge
    if find_parent(a) != find_parent(b):
        union_parent(a, b)
        total += cost
        edge_cnt += 1

    if edge_cnt == V - 1:   # 이은 간선의 수 == 정점의 수 - 1 이면 더 이상 탐색하지 않고 종료한다.
        break

print(total)
```

