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

<a href="https://www.acmicpc.net/problem/1389" target="_blank">백준 1389 케빈 베이컨의 6단계 법칙</a> 문제는 인접 리스트 BFS로 접근할 수 있다. 그런데 이 문제를 풀 때 ```런타임 에러 (IndexError)```가 발생했다. '케빈 베이컨의 수' 의 최솟값을 구하기 위해 ```nomi``` 라는 리스트에 각 정점의 케빈 베이컨의 수를 담았다.

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

<a href="https://www.acmicpc.net/problem/14442" target="_blank">백준 14442 벽 부수고 이동하기 2</a> 문제를 풀 때에는 3차원 visited 배열을 사용한다. 문제에서 최단 거리를 출력하라고 하는데, 이런 특수한 문제에서는 만약 목표 지점인 ```(N - 1, M - 1)``` 에 도달했을 때 바로 종료하는 것이 과연 맞을지 고민해봐야 한다.

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

### 백트래킹 할 때 인자가 인덱스로 작용하는지 잘 살펴봐야 한다.

<a href="https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV4suNtaXFEDFAUf" target="_blank">SWEA 1767 프로세서 연결하기</a> 문제를 풀 때 계속 제한시간 초과가 발생했다. 그 이유는 ```dfs()``` 메서드의 인자로 들어가 있는 ```n``` 이 사실상 인덱스로서 작용하고 있는데, 재귀 호출 부분에서 또 ```for j in range(E):``` 로 하여 인덱스를 사용하고 있었기 때문이다.

```python
def dfs(n, lst, v_arr, sm, core_sm):     # sm 은 전선 길이의 합
    global core_maxi, mini
    if core_sm + (E - n) < core_maxi:
        return
    if n == E:
        if core_maxi < core_sm:
            core_maxi = core_sm
            mini = sm
        elif core_maxi == core_sm:
            mini = min(mini, sm)
        return
        
    # 일단 해당 지점 전선 연결 안 하는 경우 호출한다.
    dfs(n + 1, lst, v_arr, sm, core_sm)
    ...
```

위와 같이 그냥 바로 호출하면 되는 것이었다.

<br>
<hr>

### 4차원 이상으로 visited 배열 관리하는 문제

<a href="https://www.acmicpc.net/problem/20419" target="_blank">백준 20419 화살표 미로 (Easy)</a> 문제는 상태 정보가 여러 가지이기 때문에, ```visited``` 배열을 4차원으로 관리해야 한다. 행, 열, 좌회전 횟수, 우회전 횟수의 네 가지 정보를 관리해야 하므로 4 차원으로 해야 하는 것이다.

```python
def bfs(arr, num):      # num 은 티켓 세트의 개수
    if R == 1 and C == 1:
        return True
    visited = [[[[0 for _ in range(num + 1)] for _ in range(num + 1)] for _ in range(C)] for _ in range(R)]
    queue = deque()
    visited[0][0][0][0] = 1     # (0, 0) 이고, 좌회전 : 0, 우회전 : 0
    queue.append((0, 0, 0, 0))

    step = 1

    while queue:
        step += 1
        for _ in range(len(queue)):
            cr, cc, cleft, cright = queue.popleft()
            # 일단 주어진 방향으로 갈 수 있으면 간다.
            cur_d = arr[cr][cc]
            # 좌회전 주문서, 우회전 주문서 전부를 체크해본다.
            # 0개, 0개 쓰는 경우도 있으니, 2중 for 문으로 체크한다.
            for l in range(num - cleft + 1):
                for r in range(num - cright + 1):
                    n_dir = (cur_d - l) % 4
                    n_dir = (n_dir + r) % 4     # 좌, 우 횟수만큼 회전시킨다.
                    # 그리고 방문체크배열 갈 수 있으면 간다.
                    nr = cr + dr[n_dir]
                    nc = cc + dc[n_dir]
                    if oob(nr, nc) or visited[nr][nc][cleft + l][cright + r] > 0:
                        continue
                    visited[nr][nc][cleft + l][cright + r] = 1
                    queue.append((nr, nc, cleft + l, cright + r))
                    if nr == R - 1 and nc == C - 1:
                        return True
    return False
    ...
```

<br>
<hr>

### 문제가 마치 그래프와 같은 모양이고, MST를 만들면 좋겠다는 생각이 든다면 사이클(cycle) 여부에 대해 체크해야 한다.

<a href="https://www.acmicpc.net/problem/17472" target="_blank">백준 17472 다리 만들기 2</a> 문제에서는 섬들을 정점으로 고려하고, 이 섬들을 전부 연결하는 다리 길이의 최솟값을 구해야 한다.

문제를 딱 보고 최소 신장 트리(MST)라고 생각은 했는데 그냥 안일하게 정렬한 후 작은 순으로 ```섬의 개수 - 1``` 개 만큼의 다리의 총합을 구했다가 계속 틀렸다. 왜냐하면 <b>MST에서는 사이클(cycle)이 발생하면 안 되는 것을 깜빡했기 때문</b>이다.

그래서 Kruskal's Algorithm 을 적용하여 해결하였다.

<br>
<hr>

### 현재 위치에서 좌회전, 우회전을 할 수 있다고 해서 180도 회전도 할 수 있는 것은 아니다.

<a href="https://www.acmicpc.net/problem/1726" target="_blank">백준 1726 로봇</a> 문제를 풀 때, 로봇은 ```Turn dir``` 의 명령어를 통해 좌회전 혹은 우회전을 할 수 있다고 한다. 그런데 '좌회전, 우회전을 할 수 있으니 180도 회전도 할 수 있겠지'라는 안일한 생각으로 문제를 풀었다가 계속 틀렸다.

문제에서 한 턴에 좌회전, 우회전을 할 수 있다고 해도, 한 턴에 180도 회전까지 할 수 있다고 하는지 면밀히 살펴봐야 한다. 임의로 판단을 내리면 안 되고 철저히 문제에서 언급하는 대로만 해야 하는 것이다.

<br>
<hr>

### 숫자를 정렬할 때 단지 오름차순, 내림차순이 아닌 특수한 정렬 조건을 요구하면 매우 신중하게 접근해야 한다.

<a href="https://www.acmicpc.net/problem/15663" target="_blank">N과 M (9)</a> 문제를 풀 때 숫자를 정렬해야 한다고 해서 그냥 문자열로 처리하려 했다가 계속 틀렸었다.

예를 들어 21 과 121 의 우선순위는 21 인데도, 121 이 우선으로 들어오는 문제가 있던 것이다.

<br>
<hr>

### BFS 문제를 풀 때 경계 조건을 철저히 따져야 한다.

<a href="https://www.acmicpc.net/problem/5427" target="_blank">백준 5427 불</a> 문제를 풀 때, 상근이가 처음에 격자가 주어질 때 경계값에 있으면 무조건 탈출을 할 수 있다. 그런데 이런 엣지 케이스(edge case)를 고려하지 않고 그냥 시작 (행, 열) 좌표에서 사방 탐색을 한 (행, 열) 좌표부터만 따지다 보니 계속 틀린 적이 있다.

<a href="https://www.codetree.ai/training-field/frequent-problems/problems/escape-unknown-space/description?page=1&pageSize=20" target="_blank">Code Tree 미지의 공간 탈출</a> 문제에서도 이러한 엣지 케이스가 있다. '시간의 벽' 에서 나가 '미지의 공간'으로 들어가는 딱 그 지점에 만약 탈출구가 있으면 이것을 처리해 줘야 하는데, 그 부분을 놓치면 틀리게 되는 것이다.

그러므로 BFS 문제를 풀 때에는 경계값을 무조건 고려해야 한다. 시작점이든, 끝점이든, 경계에 걸쳐있는 지점이든.

<br>
<hr>

### 문제의 문장을 엄밀하게 따져야 한다.

<a href="https://www.acmicpc.net/problem/25173" target="_blank">백준 25173 용감한 아리의 동굴 대탈출</a> 문제에서 '아리의 이동' 메서드 부분에서 '4번을 회전하고도 진행 방향을 찾지 못한 경우 현재 위치한 칸에서 아리의 이동 차례를 마친다.' 라는 조건이 있다.

이것이 진짜 회전이 4번인지, 아니면 처음 주어진 방향으로 가는 것 포함해서 네 번인지 헷갈렸다. 결국 엄밀히 따지만 처음 주어진 방향으로 한 번 나아가는 것은 회전이 아니므로, 회전을 네 번 하는 것으로 판단해 문제를 풀었다.

문장이 무언가 좀 두루뭉술하다 하더라도, 내 주관이 들어가면 안 되는 것이다.

<br>
<hr>

### BFS 에서 '가만히 있는다' 는 조건이 있으면 조심해야 한다.

<a href="https://www.acmicpc.net/problem/16954" target="_blank">백준 16954 움직이는 미로 탈출</a> 문제에서 8방 탐색 외에도 '가만히 있는다' 는 옵션이 짧게 써 있다. 이것을 고려하면 총 9 방향을 탐색하는 것이라 볼 수 있다.

이처럼 '가만히 있는다' 는 문장이 있으면 조심해야 한다. 그리고 델타 배열에 대해 ```dr = [0]``` ```dc = [0]``` 를 추가해야 한다.

<br>
<hr>

### BFS 문제에서 특정 지점에 도달했을 때 방문체크 배열을 초기화해야 하는 것은 아닌지 체크해야 한다.

<a href="https://www.acmicpc.net/problem/22944" target="_blank">백준 22944 죽음의 비</a> 문제에서는 '우산'을 집으면 우산의 내구도가 추가되면서 더 나아갈 수 있는 가능성이 생긴다.

```
6 2 5
......
......
E..S.U
......
......
......
```

그런데 이런 케이스가 있으면, 시작 지점에서 바로 끝 지점으로 갈 수는 없지만 우산이 있는 지점까지 먼저 간 다음, 우산의 내구도를 사용해 끝 지점까지 가는 것이 가능해진다. 이때 이미 방문했던 좌표들을 지나쳐야만 끝 지점으로 갈 수 있다. 즉 재방문이 허용되어야 하는 것이다.

그러므로 이런 문제와 같은 아이디어성 BFS 문제에서는 방문 체크 배열을 초기화해야 하는 순간은 없는지 면밀히 검토해야 한다.

<br>
<hr>

### 어떠한 숫자로 '나누기' 연산을 할 때에는 ZeroDivisionError 이 발생하지 않도록 조심해야 한다.

문제를 풀 때 어떤 수를 다른 수로 나누는 연산을 수행하는 경우가 있다. 이때에는 '다른 수' 가 0 이 될 가능성은 없는지 면밀히 검토해야 한다. 조건문으로 먼저 0이 되는 경우를 걸러주는 것이 안전하다.

<br>
<hr>

### 문제에서 주어지는 4 방향 혹은 8 방향의 우선순위가 있는지 반드시 살펴봐야 한다.

인접 행렬 BFS를 할 때에는 4 방향 탐색 혹은 8 방향 탐색이 거의 필수적이다. 이때 문제에서 '상 우 하 좌' 혹은 '좌 우 하 상' 과 같이 방향의 우선순위를 주는 경우가 있다. 그러면 그 우선순위를 철저히 지켜야 한다. 

그러므로 <b>방향 delta 배열을 선언할 때, 무조건 문제에서 방향 우선순위에 대해 언급하는 것은 없는지 체크해야 한다.</b>