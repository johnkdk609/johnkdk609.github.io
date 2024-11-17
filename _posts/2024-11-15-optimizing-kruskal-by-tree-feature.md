---
layout: post
title: 트리의 특성을 활용한 크루스칼 알고리즘 최적화 & 힙큐 사용에 대한 고찰
categories: Algorithm
date: 2024-11-15 23:43:00 +0900
---
크루스칼(Kruskal) 알고리즘은 MST(Minimum Spanning Tree, 최소 신장 트리)를 구현하기 위해 Union-Find 알고리즘을 활용하는 알고리즘이다. 최소 신장 트리를 구현하는 것이기 때문에 크루스칼 알고리즘에서는 트리(tree)의 특성을 사용해 최적화를 할 수 있다.

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

위 코드에서는 총 두 가지의 최적화(optimization)가 이루어졌다.

하나는 find_parent() 메서드에서 경로 압축(path compression)을 한 것이다. 그리고 다른 하나는 트리의 특성을 활용해, union_parent() 를 할 때마다 간선을 하나씩 증가시키는데 ```트리의 간선 수 == 정점의 개수 - 1``` 이 되는 순간 탐색을 종료하는 것이다. 어차피 MST 는 만들어졌고, 더 이상 볼 필요가 없는 것이다.

<br>

## Union-Find 알고리즘에서 heapq 사용에 대한 고찰

크루스칼 알고리즘은 그리디(greedy) 알고리즘의 한 종류로서 모든 간선 정보를 받은 다음, 간선의 비용이 작은 순서대로 꺼내서 사이클이 생기지 않는지 판별하고 합치는 작업을 수행한다.

이때 '간선의 비용이 가장 작은' 것부터 꺼낸다고 하니 heapq 를 사용해볼 수 있다. 그런데 과연 힙큐(heapq)를 사용하는 것이 내장 메서드 ```sort()``` 를 사용하는 것보다 효율적이라고 할 수 있을까?

힙큐의 동작 방식을 보면, 루트 노드(root node)에 최솟값이 들어가 있다. (조작을 가하면 최댓값이 들어가 있을 수도 있다.) 그래서 루트 노드의 값인 최솟값을 꺼내는 속도는 매우 빠르다. 그런데 루트 노드 외 노드들을 보면, 대체로 작은 값이 상단에 위치하지만 완전히 정렬된 것이 아니다. 만약 루트 노드의 값을 꺼내면, 힙의 동작 방식에 의해 해당 힙은 또 재정렬되는 것이다. 이 동작이 느린 것은 아니지만, 어차피 한 리스트에 대해 모든 값을 꺼낼 수도 있는 상황이라면 매번 ```heapq.heappop()```을 수행할 때마다 추가적인 작업이 이뤄지는 것이다.

이번에는 heapq 를 사용하지 않고, ```edges```라는 리스트에 ```(cost, a, b)``` 와 같은 형태로 a, b 노드 간의 가중치인 cost 정보를 튜플로 담은 다음, ```edges.sort()``` 를 했다고 생각해보자.

한 번 정렬을 했기 때문에, 이제부터는 그냥 인덱스로 순서대로 접근하면 된다. 최솟값에 접근하거나, 최솟값을 빼낼 때마다 추가적인 연산이 없고, 처음에 ```sort()```를 할 때에만 비용이 드는 것이다.

결론적으로, <b>Kruskal's Algrithm 을 작성할 때 heapq 를 사용하기보다, 그냥 ```sort()``` 를 하여 리스트에 인덱스로 접근하는 것이 더 효율적</b>이다.