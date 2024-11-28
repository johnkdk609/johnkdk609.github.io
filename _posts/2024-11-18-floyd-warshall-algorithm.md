---
layout: post
title: 플로이드 워셜 (Floyd Warshall) 알고리즘
categories: Algorithm
description: 나동빈, 2021, 「이것이 취업을 위한 코딩 테스트다 with 파이썬」, 한빛미디어.
date: 2024-11-18 20:44:00 +0900
---
플로이드 워셜 알고리즘(Floyd-Warshall Algorithm)은 '모든 지점에서 다른 모든 지점까지의 최단 경로를 모두 구해야 하는 경우' 에 사용할 수 있는 알고리즘이다. 

플로이드 워셜 알고리즘의 경우 2차원 리스트에 '최단 거리' 정보를 저장한다. 모든 노드에 대하여 다른 모든 노드로 가는 최단 거리 정보를 담아야 하기 때문이다.

노드의 개수가 N 이라고 할 때, N 번 만큼의 단계를 반복하며 '점화식에 맞게' 2차원 리스트를 갱신하기 때문에 플로이드 워셜 알고리즘은 '<b>다이나믹 프로그래밍</b>'으로 볼 수 있다.

<br>

플로이드 워셜 알고리즘의 코드는 다음과 같다.

```python
def floyd_warshall(arr):
    for k in range(1, V + 1):
        for r in range(1, V + 1):
            for c in range(1, V + 1):
                arr[r][c] = min(arr[r][c], arr[r][k] + arr[k][c])
    return arr


def print_arr(arr):
    for r in range(1, V + 1):
        for c in range(1, V + 1):
            if arr[r][c] == INF:
                print("INFINITY", end=' ')
            else:
                print(arr[r][c], end=' ')
        print()
    return


V, E = map(int, input().split())
INF = 21e8
graph = [[INF for _ in range(V + 1)] for _ in range(V + 1)]
for a in range(1, V + 1):
    graph[a][a] = 0

for _ in range(E):
    a, b, cost = map(int, input().split())
    graph[a][b] = cost

graph = floyd_warshall(graph)
print_arr(graph)
```

위 코드에 다음과 같은 테스트 케이스를 넣어보겠다.

```
4 7
1 2 4
1 4 6
2 1 3
2 3 7
3 1 5
3 4 4
4 3 2
```

그러면 다음과 같은 결과가 나온다.

<img src="https://github.com/user-attachments/assets/2a1a4183-168b-4d4a-ac35-4932a8c3bebf" width="80px" />

위 결과에서는 모든 노드에서 모든 노드로 가는 최단 거리 정보를 표현하고 있다. 예를 들어 D[13](첫 번째 행의 세 번째 열)은 8 이라는 값을 가지고 있는데, 이는 1번 노드에서 3번 노드로 가는 최단 거리가 8 이라는 의미이다.