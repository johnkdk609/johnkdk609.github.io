---
layout: post
title: 깊이 우선 탐색 (DFS) - 스택, 재귀
categories: Algorithm
description: 백준 1012. 유기농 배추
date: 2024-07-30 21:02:00 +0900
---
깊이 우선 탐색 (DFS, Depth-First Search)을 파이썬으로 구현할 때에는 두 가지 방법을 사용할 수 있다.

1. <b>스택(Stack)</b>을 활용하여 구현
2. <b>재귀(Recursion)</b>를 활용하여 구현

물론 DFS는 재귀를 활용한 방법으로 구현하는 것이 정석이고, 훨씬 더 간단하다.

하지만 경우에 따라 재귀를 하는 데에 제한이 생길 수도 있으니 스택을 활용하여 구현하는 것도 반드시 알아놔야 한다.

<br>

일단 문제는 SWEA의 깊이우선탐색 연습문제를 참고하였다. (정확한 번호를 알 수 없었다.)

다음과 같은 연결된 그래프가 있다.

<img src="https://github.com/user-attachments/assets/7269d0b6-5bcd-4469-968d-cc275ff0f66b" width="300px" />

정점들이 간선으로 연결되어 있다. 이때 화살표가 존재하지 않으므로 양방향 그래프임을 알 수 있다.

위 문제에서는 시작 정점을 1로 하고, 여러 개의 정점이 연결되어 있을 때에는 낮은 번호의 정점을 우선적으로 방문한다는 조건이 있다.

입력값은 다음과 같다.

```
1
7 8
1 2
1 3
2 4
2 5
4 6
5 6
6 7
3 7
```

그리고 출력값은 다음과 같다.

```
#1 1 2 4 6 5 7 3
```

<br>

## 스택을 활용한 DFS 구현

```python
def dfs(start):
    # [0] 필요 변수들 생성
    stack = []
    current = start
    
    # [1] 초기값 설정
    visited[current] = 1        # 방문표시 (1 : 방문)
    ans.append(current)         # 정답 관련 처리

    while True:
        # 기준 노드에서 연결된 노드 순서대로 처리
        for n in adj[current]:
            if visited[n] == 0:
                visited[n] = 1
                ans.append(n)
                
                stack.append(current)     # 기준점 이동 전 반드시 되돌아올 번호 저장
                current = n
                break
        else:                   # 더 이상 이동할 노드 없음 (막다른길 => 후진)
            if stack:
                current = stack.pop()
            else:               # 스택 비어있는 경우 => 모든 탐색 완료
                break

T = int(input().strip())
for tc in range(1, T + 1):
    V, E = map(int, input().split())
    adj = [[] for _ in range(V + 1)]
    for _ in range(E):          # 연결 정보 입력 후 표시
        s, e = map(int, input().split())
        adj[s].append(e)
        adj[e].append(s)        # 양방향 연결 그래프
        
    # 낮은 번호의 정점부터 우선적으로 방문
    for lst in adj:
        lst.sort()

    visited = [0] * (V + 1)    
    ans = []

    dfs(1)

    print(f'#{tc}', *ans)
```

<br>

## 재귀를 활용한 DFS 구현

```python
def dfs(current):
    visited[current] = 1        # 방문표시 : 재방문 방지
    ans.append(current)         # 첫 방문시 할 일 (정답처리)

    # 반복처리(탐색): 연결된 노드, 4/8 방향, 지도 정보..
    # 범위 내, 미방문, 조건(문제마다 다름)에 맞으면
    for n in adj[current]:
        if visited[n] == 0:
            dfs(n)

T = int(input())
for tc in range(1, T + 1):
    V, E = map(int, input().split())
    adj = [[] for _ in range(V + 1)]
    for _ in range(E):      # 연결 정보 입력 후 표시
        s, e = map(int, input().split())
        adj[s].append(e)
        adj[e].append(s)    # 양방향 연결 그래프

    # 낮은 번호의 정점부터 우선적으로 방문
    for lst in adj:
        lst.sort()

    ans = []
    visited = [0] * (V + 1)

    dfs(1)

    print(f'#{tc}', *ans)
```

<br>

위 문제들은 입력값을 처리할 때 인접 리스트(Adjacency List)의 방식을 사용하였다. 파이썬의 경우 인접 리스트 방식으로 DFS 문제를 해결하는 것이 매우 효율적이다.

하지만 경우에 따라서는 인접 행렬(Adjacency Matrix)의 방식을 사용할 수도 있다.

<br>

<a href="https://www.acmicpc.net/problem/1012">백준 1012번 유기농 배추</a> 문제를 보자. 여기서는 2차원 배열이 주어지고, 행, 열 순회를 하면서 깊이 우선 탐색을 해야 한다.

해당 문제에 대한 답안은 다음과 같다.

```python
import sys
sys.setrecursionlimit(10000)

dr = [-1, 1, 0, 0]      # 상 하 좌 우
dc = [0, 0, -1, 1]

def dfs(cr, cc):        # current
    visited[cr][cc] = 1
    for d in range(4):
        nr = cr + dr[d]
        nc = cc + dc[d]
        if 0 <= nr < N and 0 <= nc < M and visited[nr][nc] == 0 and field[nr][nc] == 1:
            dfs(nr, nc)    # 재귀


T = int(input())
for tc in range(1, T + 1):
    M, N, K = map(int, input().split())
    field = [[0] * M for _ in range(N)]
    for _ in range(K):
        X, Y = map(int, input().split())
        field[Y][X] = 1

    visited = [[0] * M for _ in range(N)]
    count = 0

    for r in range(N):
        for c in range(M):
            if visited[r][c] == 0 and field[r][c] == 1:    # 아직 방문 안 했고, 배추이면
                dfs(r, c)    # dfs() 메서드 동작시킨다.
                count += 1

    print(count)
```

위와 같은 방법으로, 현재의 행 인덱스(cr), 현재의 열 인덱스(cc)를 dfs() 메서드의 인자로 넣는 것이다.

<br>

## 백준 사이트에서의 재귀

직전에 본 백준 1012. 유기농 배추 문제 답안 맨 윗부분에 다음과 같은 코드가 있다.

```python
import sys
sys.setrecursionlimit(10000)
```

이는 백준 사이트가 리소스를 아끼려는 명목으로 setrecursionlimit()을 1000 정도로 낮춰놨기 때문에 따로 설정을 하는 것이다.

위 코드를 입력하지 않고 답안을 제출하면 다음과 같은 오류 메세지가 뜬다.

<img src="https://github.com/user-attachments/assets/d0742c88-eb10-44d9-a3b2-b4ba8bcb4159" width="380px" />

'RecursionError'이 발생한 것이다.

그래서 이를 해결하기 위해 위처럼 ```sys.setrecursionlimit(10000)``` 정도로 맞춰두면 재귀를 활용한 DFS 문제를 잘 통과시킬 수 있을 것이다. (10000보다 큰 수를 한다고 좋은 건 아니다. 아예 통과를 못 할 수도 있기 때문. 가령 ```sys.setrecursionlimit(1000000)```으로 설정하면 메모리 초과가 발생한다. 일반적으로 10000을 넣는다.)