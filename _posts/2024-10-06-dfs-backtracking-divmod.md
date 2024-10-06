---
layout: post
title: DFS 백트래킹에서 divmod 함수를 사용한 중복 탐색 방지
categories: Algorithm
date: 2024-10-06 17:52:00 +0900
---
백트래킹 문제를 풀다 보면, 기저조건 아래의 재귀 호출을 하는 부분에서 2차원 배열을 탐색하면서 가능한 조합(combination)을 구해야 하는 경우가 있다.

이때 <b>divmod 를 사용해 2차원 배열 중복 탐색을 방지하고 시간을 단축시킬 수 있다.</b> 2차원 배열을 마치 1차원 리스트와 같이 다루는 것으로, 조합을 구현할 때 백트래킹 메서드의 인자로 start 값을 주는 것과 유사하다.

다음은 <a href="https://www.acmicpc.net/problem/14502">백준 14502 연구소</a> 문제에서 적용한 실제 코드이다. 여기서 divmod 를 사용해서 2차원 배열 중복을 방지했다.

```python
def dfs(n, arr, start):
    global maxi
    if n == 3:
        tmp_val = bfs(arr)
        if maxi < tmp_val:
            maxi = tmp_val
        return

    for j in range(start, N * M):
        row, col = divmod(j, M)
        if arr[row][col] == 0:
            arr[row][col] = 1
            dfs(n + 1, arr, j + 1)
            arr[row][col] = 0
```

divmod 함수는 파이썬의 내장 함수로, 두 숫자를 나눌 때 몫과 나머지를 동시에 반환해준다. ```divmod(a, b)```는 ```(a // b, a % b)``` 의 결과와 같다.

위 코드에서는 divmod 를 사용하여 1차원 인덱스를 행(row)과 열(col)로 변환하였고, 2차원 배열의 중복 탐색을 효과적으로 방지하였다. 이때 divmod 의 <u>첫 번째 수로는 순회를 돌고 있는 j 를 주어야</u> 하고, <u>두 번째 수로는 2차원 배열의 열 길이를 넣어야</u> 한다.

주어진 시작 지점 start 부터 배열을 순회하면서 row 와 col 을 계산하고, 해당 위치를 방문한 뒤 다시 원래 상태로 복구해가며 DFS 를 수행한다. start 이후의 인덱스만 탐색하기 때문에 이전 위치로 돌아가지 않아 중복을 방지하는 효과가 있다.

실제로 백준에서 divmod 기법을 사용하여, 1444 ms 의 수행 시간을 412 ms 로 줄였다.