---
layout: post
title: 실수 체크리스트
categories: Algorithm
date: 2024-12-04 21:19:00 +0900
---
알고리즘 문제를 풀면서 겪은 실수들을 정리해보겠다.

### * 행, 열 좌표가 이동할 때에는 무조건 런타임 에러(Runtime Error)의 발생 가능성에 대해 면밀히 따진다.

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

<hr>

### * 상태 정보를 변경할 때 영향을 받으면 안 되는 것까지 영향을 받는지 체크해야 한다.

<a href="https://www.acmicpc.net/problem/23031" target="_blank">B23031 으어어... 에이쁠 주세요..</a> 문제에서 아리는 이동을 하면서 스위치를 켤 수 있으면 바로 켠다. 이때 스위치의 (행, 열) 위치 정보를 메인에서 그리드에 1 로 저장하고 관리했다. 만약 스위치를 켜면 켠 스위치의 자리와 스위치 기준 8방의 칸들의 불을 켜야 한다. 불이 켜진 것은 2로 표시하였다. 

그런데 이렇게 하는 경우, 만약 스위치의 8방 범위 안에 또 다른 스위치가 있으면 문제가 생긴다. 한 스위치를 켰을 때 아직 안 켜진 다른 스위치까지 상태가 2로 변경되고, 나중에 아리가 그 지점에 갔을 때 스위치가 있는지 없는지 알 수가 없게 되는 것이다.

```python
if switch_grid[ar][ac] == 1 or (ar, ac) in original_switches:       # original_switches 라는 집합(set) 자료구조를 사용하여 문제 해결
    switch_grid[ar][ac] = 2
    for d in range(8):
        snr = ar + sdr[d]
        snc = ac + sdc[d]
        if oob(snr, snc):
            continue
        switch_grid[snr][snc] = 2
```

위 코드에서는 메인에서 스위치의 (행, 열) 좌표를 받아 놓은 ```original_switches``` 라는 집합(set) 자료구조를 추가적으로 사용함으로써 이러한 문제를 해결했다. 그러므로, <b>어떠한 것의 상태 정보가 바뀔 때 바뀌면 안 되는 것들까지 같이 바뀌는지 잘 살펴보고, 그런 경우가 발생할 가능성이 있다면 추가적인 처리를 통해 영향 관계를 끊어내야 한다.</b>

