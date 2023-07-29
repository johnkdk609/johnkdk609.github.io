---
layout: post
title: DFS, BFS
description: 이것이 취업을 위한 코딩 테스트다 with 파이썬
categories: algorithm
date: 2023-07-29 23:36:00 +0900
---
탐색 알고리즘 DFS / BFS 에 대해 알아보겠다.


## DFS

DFS(Depth-First Search), 깊이 우선 탐색은 스택(stack) 자료구조를 사용한다.

구체적인 동작 방식은 다음과 같다.

1. 탐색 시작 노드를 스택에 삽입하고 방문 처리를 한다.
2. 스택의 최상단 노드에 방문하지 않은 인접 노드가 있으면 그 인접 노드를 스택에 넣고 방문 처리를 한다. 방문하지 않은 인접 노드가 없으면 스택에서 최상단 노드를 꺼낸다.
3. 2번의 과정을 더 이상 수행할 수 없을 때까지 반복한다.

(방문 처리란 스택에 한 번 삽입되어 처리된 노드가 다시 삽입되지 않게 체크하는 것을 의미한다. 방문 처리를 함으로써 각 노드를 한 번씩만 처리할 수 있다.)

DFS의 소스코드는 다음과 같다.

```python
def dfs(graph, v, visited):
    # 현재 노드를 방문처리
    visited[v] = True
    print(v, end=' ')
    # 현재 노드와 연결된 다른 노드를 재귀적으로 방문
    for i in graph[v]:
        if not visited[i]:
            dfs(graph, i, visited)

# 각 노드가 연결된 정보를 리스트 자료형으로 표현(2차원 리스트)
graph = [
    [],
    [2, 3, 8],
    [1, 7],
    [1, 4, 5],
    [3, 5],
    [3, 4],
    [7],
    [2, 6, 8],
    [1, 7]
]

# 각 노드가 방문된 정보를 리스트 자료형으로 표현(1차원 리스트)
visited = [False] * 9

# 정의된 DFS 함수 호출
dfs(graph, 1, visited)
```

위 코드의 출력 결과는 다음과 같다.

```
1 2 7 6 8 3 4 5
```


## BFS

BFS(Breadth-First Search), 너비 우선 탐색은 큐(queue) 자료구조를 사용한다.

구체적인 동작 방식은 다음과 같다.

1. 탐색 시작 노드를 큐에 삽입하고 방문 처리를 한다.
2. 큐에서 노드를 꺼내 해당 노드의 인접 노드 중에서 방문하지 않은 노드를 모두 큐에 삽입하고 방문 처리를 한다.
3. 2번의 과정을 더 이상 수행할 수 없을 때까지 반복한다.

BFS의 소스코드는 다음과 같다.

```python
from collections import deque

# BFS 메서드 정의
def bfs(graph, start, visited):
    # 큐(Queue) 구현을 위해 deque 라이브러리 사용
    queue = deque([start])
    # 현재 노드를 방문 처리
    visited[start] = True
    # 큐가 빌 때까지 반복
    while queue:
        # 큐에서 하나의 원소를 뽑아 출력
        v = queue.popleft()
        print(v, end=' ')
        # 해당 원소와 연결된, 아직 방문하지 않은 원소들을 큐에 삽입
        for i in graph[v]:
            if not visited[i]:
                queue.append(i)
                visited[i] = True

# 각 노드가 연결된 정보를 리스트 자료형으로 표현(2차원 리스트)
graph = [
    [],
    [2, 3, 8],
    [1, 7],
    [1, 4, 5],
    [3, 5],
    [3, 4],
    [7],
    [2, 6, 8],
    [1, 7]
]

# 각 노드가 방문된 정보를 리스트 자료형으로 표현(1차원 리스트)
visited = [False] * 9

# 정의된 BFS 함수 호출
bfs(graph, 1, visited)
```

위 코드의 출력 결과는 다음과 같다.

```
1 2 3 8 7 4 5 6
```


## 정리

DFS와 BFS의 구현에 대해 알아보았다. 간단히 정리하자면 다음 표와 같다.

<table>
    <tr>
        <th></th>
        <th>DFS</th>
        <th>BFS</th>
    </tr>
    <tr>
        <td>동작 원리</td>
        <td>스택</td>
        <td>큐</td>
    </tr>
    <tr>
        <td>구현 방법</td>
        <td>재귀 함수 활용</td>
        <td>큐 자료구조 이용</td>
    </tr>
</table>

코딩 테스트 중 2차원 배열에서의 탐색 문제를 만나면 그래프 형태로 바꿔서 생각하면 풀이 방법을 조금 더 쉽게 떠올릴 수 있다.

가령 게임 맵이 3×3 형태의 2차원 배열이고 각 데이터를 좌표라고 생각할 수 있는 것이다. 게임 캐릭터가 (1, 1) 좌표에 있다고 표현할 때처럼 말이다. 이때 각 좌표를 상하좌우로만 이동할 수 있다면 어떨까? 모든 좌표의 형태를 다음처럼 그래프의 형태로 바꿔서 생각할 수 있다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0863b745-47c9-4824-acd3-375e342a996d)