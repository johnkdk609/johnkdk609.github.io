---
layout: post
title: Step 단위 BFS 응용
categories: Algorithm
description: 백준 16920번 확장 게임
date: 2024-10-20 23:03:00 +0900
---
step 단위로 BFS 를 적용할 때에, 다양한 응용이 있을 수 있다.

그 중 하나는, <b>주체별로 한 턴에 퍼져야 하는 칸의 수가 다른 경우</b>가 있다. 큐(queue)에 들어간 모든 요소들이 공평하게 한 칸씩 사방 탐색을 하는 것이 아닌, 어떤 요소는 한 번 큐에서 꺼내면 사방 탐색을 하는데, 두 칸 이상씩 퍼지는 것이다. 그리고 이 한 번에 퍼지는 정도는 주체마다 다를 수 있다.

이렇게 되면 BFS 안의 BFS 를 구현해야 하는데, 각 주체별로 '한 번에 퍼져야 하는 칸의 수'가 정해져 있으므로, step 단위 BFS 를 적용할 수 있다.

대표적인 문제로는 <a href="https://www.acmicpc.net/problem/16920">백준 16920번 확장 게임</a>이 있다. 문제에서 각 플레이어는 자신의 턴이 돌아왔을 때, 자신의 성이 있는 곳에서 Si 칸 만큼 이동할 수 있다. 어떤 플레이어는 Si 가 1 이고, 어떤 플레이어는 2 등.. 자신의 차례 때 이동할 수 있는 횟수가 차이가 있는 것이다.

답안 코드는 다음과 같다.

```python
# 구상
# 각 플레이어마다 deque을 관리한다. deque 하나로 관리하는 것이 아닌, 플레이어마다 deque 한 개씩.

from collections import deque


def oob(row, col):
    return row < 0 or row >= N or col < 0 or col >= M


def castle_expand(arr):
    # 우선 플레이어 수 만큼의 deque을 담은 리스트를 생성한다.
    queue = [deque() for _ in range(P)]
    visited = [[0 for _ in range(M)] for _ in range(N)]

    # arr 행 ~ 열 순회 돌면서 각 플레이어의 시작 성 위치를 queue 에 담는다.
    for r in range(N):
        for c in range(M):
            if arr[r][c] > 0:
                queue[arr[r][c] - 1].append((r, c, 0))      # 갈 수 있는 거리도 담는다.
                visited[r][c] = arr[r][c]   # 플레이어의 번호로 담는다.

    # 이제 BFS를 돌리는데, 각 플레이어마다 갈 수 있는 범위가 다름을 명심한다.
    while any(queue):   # queue에 있는 deque 들이 하나라도 요소가 남아있다면 계속 진행
        for p_num in range(P):
            tmp_q = deque()

            while queue[p_num]:
                cr, cc, dist = queue[p_num].popleft()

                # 확장 범위까지만 진행
                if dist >= S[p_num]:
                    tmp_q.append((cr, cc, 0))    # 다음 턴으로 넘긴다.
                    continue

                # 4방향으로 확장
                for d in range(4):
                    nr = cr + dr[d]
                    nc = cc + dc[d]
                    if oob(nr, nc) or visited[nr][nc] > 0 or arr[nr][nc] == -1:
                        continue
                    visited[nr][nc] = visited[cr][cc]
                    queue[p_num].append((nr, nc, dist + 1))

            # 플레이어의 다음 턴을 위한 큐 업데이트
            queue[p_num] = tmp_q

    # 이제 전부 돌았으니, 영역 카운트 한다.
    result = [0 for _ in range(P)]
    for r in range(N):
        for c in range(M):
            if visited[r][c] > 0:
                result[visited[r][c] - 1] += 1

    return result


N, M, P = map(int, input().split())
S = list(map(int, input().split()))
grid = [[0 for _ in range(M)] for _ in range(N)]
for a in range(N):
    data = input().strip()
    for b in range(M):
        if data[b] == '.':
            grid[a][b] = 0
        elif data[b] == '#':
            grid[a][b] = -1     # 벽은 -1 로 처리
        else:
            grid[a][b] = int(data[b])

# 방향 delta - 상 좌 우 하 (문제에서 주어진 순서대로)
dr = [-1, 0, 0, 1]
dc = [0, -1, 1, 0]


result_lst = castle_expand(grid)
print(*result_lst)
```

일단 플레이어 별로 deque() 을 하나씩 만들어서 사용했다. 왜냐하면 1번 플레이어, 2번 플레이어, 3번 플레이어, ... 순으로 각자 퍼져야 하기 때문이다. 각 플레이어의 번호를 ```p_num + 1``` 이라고 할 때, ```queue[p_num]``` 에는 (행, 열, 이동 거리) 를 ```append``` 하였다. 플레이어 별로 deque() 가 있고, 각 deque() 에 값들을 담아놓았다. 이 이동 거리는 추후에 step 단위 BFS 를 할 때 사용할 것이다. 

이제 BFS 탐색을 진행하며 확장을 할 것이다. ```while any(queue):``` 로 하였는데, 이것은 '모든 플레이어 큐에서 하나라도 남아있는 경우 계속 진행한다'는 뜻이다. queue 가 플레이어 수 만큼의 deque() 들이 모여 있는 리스트이기 때문에, 단순히 ```while queue:``` 가 아니라 ```while any(queue):``` 를 한 것이다.

그리고 그 안에서 ```for p_num in range(P):``` 로 접근하여 각 플에이어 순차적으로 처리를 하였다. ```tmp_q```는 현재 턴에서 확장할 수 없거나, 더 이상 확장할 수 없는 노드를 임시로 저장하는 큐이다. 이들은 다음 턴에서 처리될 것이다.

다음으로 각 플레이어마다 BFS 를 진행한다. ```while queue[p_num]:``` 으로 하여, 각 플레이어 별 deque() 에 접근하는 것이다. ```dist```라는 변수에 거리 정보를 담았다.

```python
if dist >= S[p_num]:
    tmp_q.append((cr, cc, 0))    # 다음 턴으로 넘긴다.
    continue
```

위와 같은 코드를 통해 플레이어 별로 이동한 거리가 ```S[p_num]``` 이상이 되면(인덱스는 0부터 시작하니까), ```tmp_q``` 에 ```(cr, cc, 0)``` 을 ```append``` 한다. 거리를 다시 0 으로 초기화 하여, 다음 턴에서 처리할 노드를 ```tmp_q``` 에 담는 것이다.

아직 이동 거리가 남은 경우에는 4방 탐색을 진행한다. 조건에 부합할 경우 방문 처리를 하고, 다시 ```queue[p_num]``` 에 새로 탐색한 좌표를 담는다. 이때 거리는 ```dist + 1```을 담음으로써 step 이 증가했다는 것을 관리했다.

이 과정이 끝나면, 현재 ```queue[p_num]``` 은 비어있다. 이때, 위에서 받아놓은 ```tmp_q```를 ```queue[p_num]```에 담는다. 다음 턴에 사용할 것이기 때문이다.

<br>

각 플레이어마다 자신이 지정된 범위 내에서 단계적으로 탐색을 하고 있다. 이 코드는 각 플레이어마다 "step" 이라는 이동 제한을 걸어두고 진행하는, 단계적 BFS 로 볼 수 있다.