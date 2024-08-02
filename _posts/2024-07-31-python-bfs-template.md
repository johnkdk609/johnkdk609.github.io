---
layout: post
title: 너비 우선 탐색 (BFS)
categories: Algorithm
description: 백준 2178번 미로 탐색
date: 2024-07-31 21:59:00 +0900
---
너비 우선 탐색 (BFS, Breadth-First Search)은 깊이 우선 탐색(DFS)과 함께 그래프 탐색에서 자주 등장하는 개념이다.

BFS를 파이썬으로 구현할 때에는 큐(Queue)를 활용해야 한다. 선입선출(FIFO) 원리를 사용해야 하기 때문이다.

이러한 큐는 파이썬에서 deque 라이브러리를 사용할 수 있다. 사용 예시는 다음과 같다.

```python
from collections import deque

queue = deque()
```

이렇게 생성한 queue의 경우, 요소를 빼려면 다음과 같이 하면 된다.

```python
queue.popleft()
```

이렇게 deque 라이브러리를 사용하면 그냥 리스트를 사용하여 큐(queue)를 구현한 것보다 빠르다.

<br>

그런데 경우에 따라서는 deque 라이브러리를 사용하지 못할 수도 있다. (코딩테스트 주최 측에서 해당 라이브러리를 금지시켜둔 경우 등..)

그러므로 그냥 리스트(list)를 사용해 큐를 구현하는 방법도 알아야 한다.

## BFS 구현

직전에 공부한 DFS에서 활용한 SWEA 문제를 똑같이 사용해보겠다.

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
#1 1 2 3 4 5 7 6
```

<br>

위 문제에 대한 BFS 구현은 다음과 같다.

```python
def bfs(start):
    # [0] queue, visited[], 필요변수 생성
    queue = []
    visited = [0] * (V + 1)     # 메인에서 만들어도 상관 없지만, 아무튼 BFS는 여기서 도는 것이니까
    
    # [1] 큐에 초기 데이터(들) 삽입(+ 방문표시..)
    queue.append(start)
    visited[start] = 1
    ans.append(start)

    while queue:    # 큐에 데이터가 있는 동안 반복처리
        current = queue.pop(0)      # 큐에서 기준 데이터 꺼냄
        # 반복: 연결, 4/8방향..
        # 범위 내, 미방문, *조건 맞으면 queue 에 삽입
        for n in adj[current]:
            if visited[n] == 0:
                queue.append(n)
                visited[n] = 1
                ans.append(n)


T = int(input())
for tc in range(1, T + 1):
    V, E = map(int, input().split())
    adj = [[] for _ in range(V + 1)]
    for _ in range(E):
        s, e = map(int, input().split())
        adj[s].append(e)
        adj[e].append(s)        # 양방향

    for lst in adj:
        lst.sort()

    ans = []
    bfs(1)

    print(f'#{tc}', *ans)
```

위 코드에서 ```current = queue.pop(0)``` 부분을 자세히 보자. 원래 ```.pop()``` 으로 입력하면 리스트의 맨 뒤쪽에 있는 요소를 제거하게 된다. default 값으로 괄호 안에 '-1'이 들어있기 때문이다.

하지만 <b>맨 앞쪽에 있는 요소를 빼려면 ```.pop(0)```으로 인덱스 '0'을 명시해야 한다.</b>

(이 부분을 놓치지 않게 주의해야 한다!)

<br>

## 2차원 배열 BFS 구현

위의 예시는 인접 리스트에 대해 BFS 구현을 진행한 것이다.

만약 인접 리스트가 아니라 인접 배열을 다뤄야 한다면 큐에 행, 열의 좌표를 넣는 방식으로 접근하면 된다.

일단 문제는 <a href="https://www.acmicpc.net/problem/2178">백준 2178번 미로 탐색</a> 문제이다.

답안 코드는 다음과 같다.

```python
def bfs(sr, sc):
    queue = []
    visited[sr][sc] = 1     # 방문 처리 후
    queue.append((sr, sc))  # 큐에 삽입

    while queue:
        cr, cc = queue.pop(0)   # 현재 행, 열 좌표
        for d in range(4):      # 사방탐색
            nr = cr + dr[d]
            nc = cc + dc[d]
            if 0 <= nr < N and 0 <= nc < M and visited[nr][nc] == 0 and maze[nr][nc] == 1:  # 경계 벗어나지 않고, 아직 방문 안 했으며, 미로에서 1이라면
                visited[nr][nc] = visited[cr][cc] + 1   # 방문 체크를 하는데, 시작 지점에서부터 거리 구해야 하니까 이렇게 설정
                queue.append((nr, nc))      # 큐에 삽입

# 사방탐색 - 상 하 좌 우
dr = [-1, 1, 0, 0]
dc = [0, 0, -1, 1]

N, M = map(int, input().split())
maze = [[0] * M for _ in range(N)]
for r in range(N):
    data = input().strip()
    for c in range(M):
        maze[r][c] = int(data[c])

visited = [[0] * M for _ in range(N)]   # 방문 체크를 위한 2차원 배열

bfs(0, 0)   # 시작 지점인 0, 0 인덱스에서 bfs() 메서드 실행
print(visited[N - 1][M - 1])    # (0, 0) 과 (N - 1, M - 1) 인덱스 값까지 다 포함해야 하므로.
```

위와 같이 델타 배열을 만들고, 사방 혹은 팔방 탐색을 하면서 특정 조건에 부합하는 경우 큐에 삽입을 하는 것이다.

<br>

## BFS 인사이트

어떤 노드에서 다른 노드까지의 최소 거리를 구하는 문제가 나온다면 BFS라는 생각을 해볼 수 있다.

위 백준 2178 미로 탐색 문제의 경우에도 '지나야 하는 최소 칸 수'를 구하는 문제였다.

```python
if 0 <= nr < N and 0 <= nc < M and visited[nr][nc] == 0 and maze[nr][nc] == 1:
    visited[nr][nc] = visited[cr][cc] + 1
```

위 코드에서 이렇게 경계 조건, 방문 여부, 경로 여부 등을 검토하고 문제가 없으면 방문 체크를 하였다.

그런데 방문 체크를 할 때 단순히 ```visited[nr][nc] = 1```이 아닌, 직전에 queue에서 pop(0) 된 것의 값에서 1을 더하였다.

이렇게 하면 시작 지점에서 끝 지점까지의 '최소 거리'를 확보할 수 있다. 너비 우선 탐색을 하면서 주변에 가능한 곳일 경우 동일하게 1씩 증가하는 것이기 때문이다.

<br>

방문 체크를 하기 위해 생성하는 <b>visited 배열(2차원) 혹은 리스트(1차원)를 활용하면 두 노드 간의 거리를 구할 수 있다.</b>