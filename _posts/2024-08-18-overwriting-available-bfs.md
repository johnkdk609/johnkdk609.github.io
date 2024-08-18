---
layout: post
title: 중복 방문이 가능한 BFS
categories: Algorithm
description: SWEA 1249 보급로
date: 2024-08-18 23:02:00 +0900
---
일반적으로 BFS (너비 우선 탐색) 문제를 풀 때에는 visited 배열을 사용할 때 처음에는 0, 방문하면 1 로 체크하여 다시 방문하지 않게 한다. (False로 초기화 했다가 True로 방문 체크를 하는 경우도 있다.)

만약 최단 거리를 구하는 문제라면, visited 배열을 시작 지점부터 level 별로 1 씩 증가하게 설정을 하는 방식으로 BFS 를 사용하기도 한다.

그런데 경우에 따라서는 다른 방식으로 visited 배열을 관리해야 할 수도 있다.

<br>

## 중복 방문이 가능한 BFS

중복 방문이 가능한 BFS 란, 어떤 지점에 한 번 방문하면 다시 방문을 못 하는 것이 아닌, 다시 방문을 할 수 있다는 얘기이다.

한 번 이미 방문한 지점을 또 들러야 하는 경우는 무엇이 있을까?

<b>가중치(weight)가 존재하는 경우에 재방문을 허용해야 할 수 있다.</b>

<br>

SWEA 1249번 보급로 문제의 경우 이렇게 중복 방문이 가능한 BFS 의 방식으로 접근을 해야 한다.

다음 그림을 보자.

<img src="https://github.com/user-attachments/assets/0087e55c-5134-4b70-99d6-df21f9ebab2b" width="200px">

시작 지점(S)에서 도착 지점(G)까지 가야 하는데, 각 지점을 지날 때마다 그 지점에 있는 숫자만큼 복구 시간이 증가하고, 총 복구 시간의 최솟값을 구해야 하는 상황이다. 그러면 각 좌표에 있는 숫자들 즉 '가중치'를 고려해서 경로를 골라야 하는 것이다. 위 그림의 경우 S 부터 G 까지 회색 칠해진 경로를 통해서 가야 최단 복구 시간을 구할 수 있다.

답안 코드는 다음과 같다.

```python
from collections import deque
 
def bfs(sr, sc):    # 중복 가능한 BFS
    queue = deque()
    visited[sr][sc] = region[sr][sc]    # 시작 지점의 주어진 값으로 산입
    queue.append((sr, sc))
 
    while queue:
        cr, cc = queue.popleft()
        for d in range(4):
            nr = cr + dr[d]
            nc = cc + dc[d]
            if 0 <= nr < N and 0 <= nc < N and visited[cr][cc] + region[nr][nc] < visited[nr][nc]:       # visited 배열 업데이트 가능하도록
                visited[nr][nc] = visited[cr][cc] + region[nr][nc]      # 더 작은 값으로 업데이트
                queue.append((nr, nc))
 
# 델타 탐색 - 상 하 좌 우
dr = [-1, 1, 0, 0]
dc = [0, 0, -1, 1]
 
T = int(input())
for tc in range(1, T + 1):
    N = int(input())
    region = [[0] * N for _ in range(N)]
    for r in range(N):
        data = input().strip()
        for c in range(N):
            region[r][c] = int(data[c])
 
    INF = 10 * 100 * 100    # 초기값을 Infinite 로 채운다.
    visited = [[INF] * N for _ in range(N)]
 
    bfs(0, 0)
    print(f'#{tc} {visited[N - 1][N - 1]}')
```

BFS 메서드를 짤 때, nr, nc 의 조건을 체크해야 한다. 일반적인 BFS 라면 경계에 벗어나지 않는지, 아직 방문한 지점이 아닌지 등의 조건들을 살필 것이다. 하지만 이 문제의 경우, ```visited[cr][cc] + region[nr][nc] < visited[nr][nc]``` 라는 조건을 추가했다.

즉, queue에서 popleft 한 좌표(cr, cc)까지의 최단 복구 시간에 현재 도달하고자 하는 지점(nr, nc)의 복구 시간을 더한 값이, 현재 visited 배열에 저장돼 있는 해당 지점(nr, nc)의 복구 시간보다 짧다면, visited 배열의 (nr, nc) 지점의 값을 더 작은 값으로 변경해주는 것이다.

이때 중요한 점은, '더 작은 값'으로 업데이트를 하기 위해서 처음에 visited 배열을 초기화할 때, 각 좌표의 값으로 0 과 같은 수가 아닌, 그 칸에 들어갈 수 있는 가장 큰 값을 넣는 것이다. 그래야 처음부터 더 작은 값으로 업데이트를 할 수 있기 때문이다. 위 코드의 경우 가장 큰 수 INF를 10 x 100 x 100 으로 하였다. (그런데 ```21e8``` 을 INF 값으로 많이 사용하곤 한다.)

이렇게 기존의 BFS 메서드에서 약간의 변경을 함으로써 visited 배열에 중복 방문을 가능하게 하였고, 가중치에 대한 고려를 추가할 수 있었다.