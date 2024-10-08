---
layout: post
title: 최단 경로(Shortest Path)를 반환하는 BFS
categories: Algorithm
date: 2024-10-08 13:35:00 +0900
---
BFS 알고리즘 문제를 풀 때에는 문제에서 '최단 거리(shortest distance)'를 요구하는 경우가 많다. 그런데 어떤 경우에는 <b>'최단 경로(shortest path)'</b>를 요구하기도 한다.

최단 경로를 고르기 위해서는 대체로 문제에서 방향 우선순위를 조건으로 제시해 준다. 왜냐하면 상, 하, 좌, 우 우선순위가 없다면 최단 경로는 여러 가지가 될 수 있기 때문이다. 만약 시작 지점 A 에서 목표 지점 B까지 가는 방법이 여러 개 있다고 하면, 내가 방향을 순서대로 상, 하, 좌, 우 순으로 설정할 때와 우, 하, 상, 좌 순으로 설정할 때 도출되는 최단 경로는 다를 가능성이 매우 높다.

<b>핵심은 queue 에 append 할 때 경로(path)도 함께 넣어주는 것이다.</b> 일반적인 BFS에서는 (행, 열) 만 queue 에 append 한다. 하지만, 경로를 (행, 열) 정보에 추가해 담아서 탐색된 모든 칸이 '자기 자신까지 오는 최단 경로' 정보를 가지고 있게 되는 것이다.

<a href="https://www.codetree.ai/training-field/frequent-problems/problems/destroy-the-turret/description?page=3&pageSize=5">코드트리 포탑 부수기</a> 문제에서 이러한 '최단 경로(shortest path)'를 찾을 것을 요구한다. 공격자가 '레이저 공격'을 할 때, 피격자까지 이어지는 최단 경로에 있는 포탑들도 피격자가 받는 공격의 절반만큼 공격을 받는 것이다.

<br>

최단 경로를 반환하는 알고리즘 코드는 다음과 같다.

```python
# 레이저 공격과 포탄 공격을 판별하기 위해서는 특수한 BFS 를 사용해야 한다.
# 배열의 양 끝이 연결되는 방문체크배열 => 그리고 중복 가능한 방문체크로 업데이트 가능해야 한다.
# 최솟값으로 업데이트 하므로 초반에 INF 값 설정해주는 게 좋겠다.

from collections import deque


def bfs(sr, sc, er, ec, arr):
    visited = [[INF for _ in range(M)] for _ in range(N)]   # N × M 크기 2차원 배열
    queue = deque()
    visited[sr][sc] = 1

    path = [(sr, sc)]       # 경로 정보 담을 리스트 path

    queue.append((sr, sc, path))      # 최단 경로를 받기 위해 리스트도 함께 넣는다.

    while queue:
        cr, cc, path = queue.popleft()
        for d in range(4):
            nr = (cr + laser_dr[d]) % N     # 이 문제에서는 끝과 끝이 연결돼 있기 때문
            nc = (cc + laser_dc[d]) % M     # 상동
            # 부서진 포탑은 방문하지 않는다.
            if arr[nr][nc] == 0:
                continue
            # 더 작은 값이면 업데이트
            if visited[nr][nc] > visited[cr][cc] + 1:
                visited[nr][nc] = visited[cr][cc] + 1       # 끝과 끝이 연결돼 있기 때문에 최솟값 갱신을 해줘야 한다.

                if nr == er and nc == ec:   # 목표 지점에 도달하면
                    path += [(nr, nc)]      # 마지막 지점까지 경로에 담아서
                    return True, path       # '연결돼 있다' 는 취지의 True 와 경로를 함께 리턴한다.

                queue.append((nr, nc, path + [(nr, nc)]))       # 목표 지점에 도달한 것이 아니면 그냥 queue 에 새로 추가한다. 이때 새로 발견한 path 도 함께 담는다.

    return False, None      # 만약 아무리 탐색해도 목표 지점에 닿을 수 없으면 False와 None을 리턴한다.


N, M, K = map(int, input().split())     # K 는 턴 수
grid = []       # N × M 격자
for _ in range(N):
    grid.append(list(map(int, input().split())))
# 4 ≤ N, M ≤ 10
# 1 ≤ K ≤ 1,000
# 0 ≤ 공격력 ≤ 5,000

# BFS 중복방문허용을 위한 매우 큰 수 INF
INF = 100000

# 레이저 BFS 델타 - 우 하 좌 상 (순서대로) ⇒ 문제에서 주어진 조건
laser_dr = [0, 1, 0, -1]
laser_dc = [1, 0, -1, 0]


ans, lst = bfs(0, 1, 2, 3, grid)
if ans:
    print(lst)
else:
    print('그런 경로는 존재하지 않습니다.')
```

위 코드에 다음과 같은 테스트케이스를 넣어보겠다.

```
4 4 1
0 1 4 4
8 0 10 13
8 0 11 26
0 0 0 0
```

출력 결과는 다음과 같다.

```
[(0, 1), (0, 2), (0, 3), (1, 3), (2, 3)]
```

(0, 1) 에서 (2, 3) 까지 가는 최단 경로가 출력된 것을 알 수 있다. path 라는 리스트를 사용해 새로운 지점을 탐색할 때마다, 현재 queue 에서 ```popleft()``` 한 요소들 중 path 에 그 새로운 지점 정보를 넣고, 다시 queue 에 ```append``` 하는 것이다. 그리고 <u>새로 탐색한 지점이 목표 지점을 방문하는 순간 그 경로를 리턴하면 된다.</u> 이때 새로 탐색한 지점까지 추가해서 리턴해야 시작 지점부터 끝 지점까지 전부 포함된 경로를 받을 수 있다. 

목표 지점을 방문하는 순간 그 path를 리턴하면, 굳이 뒤에서 다른 경로를 탐색하지 않기 때문에 시간 측면에서도 효율적일 수 있다.

<b>대부분의 BFS 응용은 queue 에 어떤 정보를 어떻게 담느냐로 이루어진다.</b> BFS를 써야 하는데 아이디어가 필요한 부분이라면, queue 에 요소를 삽입할 때 어떤 조건을 생각해야 할지, 아니면 어떤 정보를 같이 queue 에 담을지 고민하면 되겠다.