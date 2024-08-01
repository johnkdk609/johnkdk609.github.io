---
layout: post
title: BFS 두 개 이상 지점에서 순회 도는 문제
categories: Algorithm
description: 백준 7576 토마토
date: 2024-08-01 21:50:00 +0900
---
BFS 문제를 풀 때에 이전과 같은 방법으로 시작 행 좌표, 시작 열 좌표를 인자로 두는 메서드 방식을 자주 사용하곤 한다.

그런데 어떤 2차원 배열이 있을 때, 두 개 이상의 좌표에서 (거의 동시에) 순회를 돌아야 하는 경우가 있다.

이 경우에 이전의 방식을 사용하면 마땅히 해결하기 쉽지 않다. 왜냐하면 두 시작 지점이 서로 연결될 수 있다면, 먼저 BFS 메서드를 돌린 곳에서 다음 시작 지점까지 전부 방문 처리를 해버리기 때문이다.

<br>

<a href="https://www.acmicpc.net/problem/7576">백준 7576 토마토</a> 문제가 정확한 예시이다.

N x M 크기의 2차원 배열에 익은 토마토는 1, 안 익은 토마토는 0, 토마토가 안 들어있는 경우는 -1 이 배치되어 있다.

안 익은 토마토(0)들은 익은 토마토(1) 주변에 있어야만 익는다는 가정이 있다. 만약 안 익은 토마토가 익은 토마토에 전혀 닿아있지 않다면 끝까지 안 익는 것이다.

<br>

이 경우 BFS를 사용할 수 있다. 그런데 익은 토마토(1)가 2개 이상이면 좀 더 고려해야할 것이 있다.

토마토가 모두 익을 때까지의 최소 날짜를 출력해야 한다면, BFS를 이용하여 방문 처리를 할 때 큐에서 직전에 나온 좌표값 + 1 을 하여 점진적으로 증가시켜 알아낼 수 있다.

<br>

백준 7576 토마토 문제의 답안은 다음과 같다.

```python
from collections import deque   # 속도를 개선하기 위해 deque 라이브러리 사용

def bfs():
    queue = deque()
    cnt = 0     # 안 익은 토마토가 마지막에 있는지 확인하기 위해 생성한 변수
    for r in range(N):      # 일단 순회 돌면서 익은 토마토를 큐에 넣고, 해당 지점을 방문 체크한다.
        for c in range(M):
            if toms[r][c] == 1:
                queue.append((r, c))
                visited[r][c] = 1
            elif toms[r][c] == 0:   # 안 익은 토마토는 일단 전부 카운트 한다.
                cnt += 1

    while queue:
        cr, cc = queue.popleft()
        for d in range(4):      # 사방탐색
            nr = cr + dr[d]
            nc = cc + dc[d]
            if 0 <= nr < N and 0 <= nc < M and visited[nr][nc] == 0 and toms[nr][nc] == 0:      # 경계 안 벗어나고, 방문 안 했고, 안 익은 토마토의 경우
                visited[nr][nc] = visited[cr][cc] + 1   # 방문 체크를 하는데, '최소 거리'를 구하는 것이기 때문에 이렇게 설정
                queue.append((nr, nc))      # 큐에 삽입
                cnt -= 1                    # 안 익은 토마토가 익었으니 cnt에서 1을 뺀다.

    if cnt > 0:     # 안 익은 토마토가 남아있으면
        return -1   
    else:
        return visited[cr][cc] - 1      # 큐가 빌 때까지 진행하면 가장 마지막에 꺼낸 좌표가 가장 멀리 있다.

# 사방 탐색을 위한 delta - 상 하 좌 우
dr = [-1, 1, 0, 0]
dc = [0, 0, -1, 1]

M, N = map(int, input().split())
toms = []     # 토마토 상자
for _ in range(N):
    toms.append(list(map(int, input().split())))

visited = [[0] * M for _ in range(N)]       # 방문 체크를 위한 2차원 배열

ans = bfs()
print(ans)
```

일단 시간을 개선하기 위해 파이썬의 deque 라이브러리를 import 했다. deque 이기 때문에 ```.pop(0)``` 이 아닌, ```.popleft()``` 을 기입해야 한다.

우선 행, 열 전체 순회를 돌면서 큐(queue)에 익은 토마토(1)들의 좌표를 전부 순서대로 넣는다. 그리고 넣자마자 방문 처리를 위해 생성한 visited 배열의 해당 지점을 1로 업데이트 한다.

그리고 동일하게 ```while queue:``` 의 방식을 이용해 BFS를 동작시키는 것이다.

그리고 BFS 로직을 메서드로 모듈화 하였는데, 큐가 빌 때까지 진행하면 가장 마지막에 꺼낸 좌표가 가장 시작 지점에서 멀리 있다. 그래서 ```return visited[r][c] - 1``` 을 입력하였다. 