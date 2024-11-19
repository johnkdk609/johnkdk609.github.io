---
layout: post
title: 큐 2개 사용하는 BFS
categories: Algorithm
date: 2024-11-19 13:39:00 +0900
---
BFS 문제는 다양한 양상으로 응용이 된다. 파이썬의 deque 라이브러리를 사용한다면 ```queue = deque()``` 의 방식으로 해서 큐를 생성하고, ```queue.popleft()``` 를 하면서 BFS를 동작시킨다.

일반적으로는 큐를 하나만 생성하여 문제를 푸는 경우가 많다. 그런데 경우에 따라서 <b>큐를 두 개 사용하여</b> 보다 명확하고 깔끔하게 풀 수 있기도 하다. <u>큐를 두 개 사용할 경우, step BFS 의 방식을 사용해야 하는 경우가 많다.</u>

<br>

<a href="https://www.acmicpc.net/problem/5427" target="_blank">백준 5427번 불</a> 문제가 큐 두 개를 사용하여 깔끔하게 풀 수 있는 대표적인 문제이다.

답안 코드는 다음과 같다.

```python
# 큐 두 개를 사용하는 BFS를 해보겠다.

from collections import deque


def oob(row, col):
    return row < 0 or row >= H or col < 0 or col >= W


def bfs(arr, sr, sc):
    visited = [[0 for _ in range(W)] for _ in range(H)]
    s_queue = deque()
    f_queue = deque()
    for r in range(H):
        for c in range(W):
            if arr[r][c] == '*':
                visited[r][c] = -1
                f_queue.append((r, c))
    visited[sr][sc] = 1
    s_queue.append((sr, sc))

    step = 1
    escaped = False

    while s_queue:
        step += 1
        for _ in range(len(f_queue)):
            cr, cc = f_queue.popleft()
            for d in range(4):
                nr = cr + dr[d]
                nc = cc + dc[d]
                if oob(nr, nc) or arr[nr][nc] == '#' or visited[nr][nc] < 0:   # 불의 경우 불이 지나간 자리는 재방문 불가능하게 한다. 상근이가 지나간 자리는 방문 가능. (덮어씌우기)
                    continue
                visited[nr][nc] = -1
                f_queue.append((nr, nc))

        for _ in range(len(s_queue)):
            cr, cc = s_queue.popleft()
            for d in range(4):
                nr = cr + dr[d]
                nc = cc + dc[d]
                if oob(nr, nc) or arr[nr][nc] == '#' or visited[nr][nc] != 0:
                    continue
                visited[nr][nc] = step
                s_queue.append((nr, nc))
                if nr == 0 or nr == H - 1 or nc == 0 or nc == W - 1:
                    escaped = True
                    return escaped, step

    return escaped, step


# 동서남북 4방향 delta
dr = [0, 0, 1, -1]
dc = [1, -1, 0, 0]


T = int(input())
for tc in range(1, T + 1):
    W, H = map(int, input().split())
    grid = []
    for _ in range(H):
        grid.append(list(input().strip()))

    # '.': 빈 공간
    # '#': 벽
    # '@': 상근이의 시작 위치
    # '*': 불
    # 각 지도에 @의 개수는 하나이다.

    sgr, sgc = -1, -1       # 상근 (행, 열)
    breaker = False
    for a in range(H):
        if breaker:
            break
        for b in range(W):
            if grid[a][b] == '@':
                sgr, sgc = a, b
                breaker = True
                break

    if sgr == 0 or sgr == H - 1 or sgc == 0 or sgc == W - 1:
        print(1)
    else:
        flag, ans = bfs(grid, sgr, sgc)
        if flag:
            print(ans)
        else:
            print("IMPOSSIBLE")
```

문제의 주인공인 상근이의 이동 경로는 ```s_queue```로, 불이 퍼지는 이동 경로는 ```f_queue``` 로 관리했다. 한 스텝에 대해 불이 먼저 한 칸씩 퍼지고, 나머지 이동할 수 있는 곳으로 상근이가 한 칸 이동하는 것이다.

이때 불이 있는 지점은 상근이가 갈 수 없다는 점에서, 불은 방문 체크 배열에서 -1 인 음수로 채웠다. 반대로 상근이는 '최단 시간'을 구해야 하므로 ```step``` 값을 넣었다. (step은 한 스텝마다 1씩 커진다.)

<br>

만약 하나의 큐로 상근이와 불을 전부 관리하려면, ```queue.appendleft()``` 와 같은 방식으로 불을 상근이보다 앞에 넣어야 한다. 그런데 만약 코드가 매우 복잡해지면 이렇게 하나의 큐로 전부 관리하면 상당히 정신 없어진다. (코드가 복잡해지면 실수가 발생할 가능성이 높아진다.) 

불을 관리하는 큐, 상근이를 관리하는 큐로 구분해서 사용하면 보다 깔끔하게 코드를 짤 수 있는 것이다.

한편, 메인 부분에서 ```bfs()``` 메서드를 호출하기 전에, 우선 입력값으로 주어진 상근이의 위치가 배열의 경계에 있는지를 먼저 체크했다. 만약 경계에 있다면 무조건 탈출 가능한 것이기 때문이다.