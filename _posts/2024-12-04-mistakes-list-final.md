---
layout: post
title: 실수 체크리스트
categories: Algorithm
date: 2024-12-04 21:19:00 +0900
---
알고리즘 문제를 풀면서 겪은 실수들을 정리해보겠다.

### 행, 열 좌표가 이동할 때에는 무조건 런타임 에러(Runtime Error)의 발생 가능성에 대해 면밀히 따진다.

<a href="https://www.codetree.ai/training-field/frequent-problems/problems/hide-and-seek/description?page=2&pageSize=20" target="_blank">Code Tree 술래잡기</a> 문제에서, 술래가 본인 칸 포함 현재 방향의 3 개 칸을 검토한다. 이때 반복문으로 다음 칸을 탐색했는데, ```oob()``` 체크를 깜빡했다가 런타임 에러 중 ```IndexError: list index out of range``` 가 발생했다.

```python
def catcher_catch(arr, turn):
    cnt = 0
    for i in range(3):
        nr = sr + i * dr[s_dir]
        nc = sc + i * dc[s_dir]
        if oob(nr, nc):     # 이 부분을 놓쳤다가 런타임 에러 발생
            break
        if tree_grid[nr][nc] == 1:
            continue
    ...
```

<b>행, 열 좌표를 이동하면서 무언가를 할 때에는 무조건 경계 체크(out of bounds)를 먼저 한다.</b>

<br>
<hr>

### 상태 정보를 변경할 때 영향을 받으면 안 되는 것까지 영향을 받는지 체크해야 한다.

<a href="https://www.acmicpc.net/problem/23031" target="_blank">백준 23031 으어어... 에이쁠 주세요..</a> 문제에서 아리는 이동을 하면서 스위치를 켤 수 있으면 바로 켠다. 이때 스위치의 (행, 열) 위치 정보를 메인에서 그리드에 1 로 저장하고 관리했다. 만약 스위치를 켜면 켠 스위치의 자리와 스위치 기준 8방의 칸들의 불을 켜야 한다. 불이 켜진 것은 2로 표시하였다. 

그런데 이렇게 하는 경우, 만약 스위치의 8방 범위 안에 또 다른 스위치가 있으면 문제가 생긴다. 한 스위치를 켰을 때 아직 안 켜진 다른 스위치까지 상태가 2로 변경되고, 나중에 아리가 그 지점에 갔을 때 스위치가 있는지 없는지 알 수가 없게 되는 것이다.

```python
    ...
if switch_grid[ar][ac] == 1 or (ar, ac) in original_switches:       # original_switches 라는 집합(set) 자료구조를 사용하여 문제 해결
    switch_grid[ar][ac] = 2
    for d in range(8):
        snr = ar + sdr[d]
        snc = ac + sdc[d]
        if oob(snr, snc):
            continue
        switch_grid[snr][snc] = 2
    ...
```

위 코드에서는 메인에서 스위치의 (행, 열) 좌표를 받아 놓은 ```original_switches``` 라는 집합(set) 자료구조를 추가적으로 사용함으로써 이러한 문제를 해결했다. 그러므로, <b>어떠한 것의 상태 정보가 바뀔 때 바뀌면 안 되는 것들까지 같이 바뀌는지 잘 살펴보고, 그런 경우가 발생할 가능성이 있다면 추가적인 처리를 통해 영향 관계를 끊어내야 한다.</b>

<br>
<hr>

### 그리디 문제가 아니라면, 그리디한 생각은 하지 않는다.

<a href="https://school.programmers.co.kr/learn/courses/30/lessons/60063" target="_blank">프록그래머스 블록 이동하기</a> 문제에서 로봇은 한 번에 두 칸을 차지하고 있고, 각 칸을 기준으로 좌회전, 우회전을 할 수 있으며 그냥 상하좌우로 이동할 수도 있다. 이렇게 보면 단순하게 8가지의 움직임이 있다는 것을 알 수 있다.

그런데 이 문제를 풀 때, 한 칸만 기준으로 보면 더 효율적일 것이라 생각했다. 한 칸 기준으로 보면 좌회전, 우회전, 그리고 사방 이동으로 총 6 가지이다. 그리디적인 사고를 하고 문제를 풀었는데 계속 틀렸다.

두 칸을 차지하니까 두 개 칸을 기준점으로 놓고 보면 되는 것이다. (물론 큐에 넣는 것은 하나만 넣기는 해야 한다.) 문제의 요구사항 그대로 구현을 하고 제출하니 통과되었다.

그러므로 <b>그리디 유형이 아니라면 그리디적인 사고방식은 하지 않는다. 문제에서 요구하는 것을 그대로 구현해야 한다.</b>

<br>
<hr>

### 인접 리스트 BFS 에서 정점의 번호를 1 부터 시작하게 할 때, 런타임 에러에 주의한다.

<a href="https://www.acmicpc.net/problem/1389">백준 1389 케빈 베이컨의 6단계 법칙</a> 문제는 인접 리스트 BFS로 접근할 수 있다. 그런데 이 문제를 풀 때 ```런타임 에러 (IndexError)```가 발생했다. '케빈 베이컨의 수' 의 최솟값을 구하기 위해 ```nomi``` 라는 리스트에 각 정점의 케빈 베이컨의 수를 담았다.

그런데 정점이 1부터 시작한다는 것을 고려했을 때 맨 앞에 더미 데이터 0 을 넣으면 ```min()``` 함수로 가장 작은 값을 구할 때 0 이 나올 것이기 때문에 그냥 더미 데이터를 빼고 담았다.

```python
nomi = []
for a in range(1, N + 1):
    nomi.append(adj_bfs(adj, a))

for a in range(1, N + 1):       # nomi 리스트는 0 ~ N - 1 까지밖에 없는데, 1 ~ N 을 탐색하려고 하니 런타임 에러(IndexError)가 발생한 것이다.
    if nomi[a] == min(nomi):
        print(a + 1)
        break
```

이런 사소한 실수를 해서는 안 된다.

<br>
<hr>

### BFS 에서 목표 지점에 도달하자마자 종료하는 것이 바람직한지 생각해봐야 한다.

<a href="https://www.acmicpc.net/problem/14442">백준 14442 벽 부수고 이동하기 2</a> 문제를 풀 때에는 3차원 visited 배열을 사용한다. 문제에서 최단 거리를 출력하라고 하는데, 이런 특수한 문제에서는 만약 목표 지점인 ```(N - 1, M - 1)``` 에 도달했을 때 바로 종료하는 것이 과연 맞을지 고민해봐야 한다.

```python
if oob(nr, nc):
    continue
if ch < K and arr[nr][nc] == 1 and visited[nr][nc][ch + 1] == 0:
    visited[nr][nc][ch + 1] = step
    queue.append((nr, nc, ch + 1))
    # if nr == N - 1 and nc == M - 1:           # 목표 지점에 도달하면 바로 종료를 했는데, 이것 때문에 틀렸다.
    #     return step
elif arr[nr][nc] == 0 and visited[nr][nc][ch] == 0:
    visited[nr][nc][ch] = step
    queue.append((nr, nc, ch))
    # if nr == N - 1 and nc == M - 1:
    #     return step
```

위 코드에서 주석 처리된 부분을 주석을 푼 채로 제출했을 때 틀렸다. 왜냐하면 모든 벽 부순 횟수(```ch```)에 대해 최솟값을 확인해야 하는데, 아직 미처 확인하지도 못한 상황에서 바로 종료를 했기 때문이다.

그러므로, <b>BFS 문제를 풀 때 목표 지점에 도달했을 때 바로 종료해도 문제가 없는지 세심하게 따져야 한다.</b>

<br>
<hr>

