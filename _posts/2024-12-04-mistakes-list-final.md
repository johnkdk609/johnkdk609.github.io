---
layout: post
title: 실수 체크리스트
categories: Algorithm
date: 2024-12-04 21:19:00 +0900
---
알고리즘 문제를 풀면서 겪은 실수들을 정리해보겠다.

### * 행, 열 좌표가 이동할 때에는 무조건 런타임 에러(Runtime Error)의 발생 가능성에 대해 면밀히 따진다.

Code Tree 술래잡기 문제에서, 술래가 본인 칸 포함 현재 방향의 3 개 칸을 검토한다. 이때 반복문으로 다음 칸을 탐색했는데, ```oob()``` 체크를 깜빡했다가 런타임 에러 중 ```IndexError: list index out of range``` 가 발생했다.

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

