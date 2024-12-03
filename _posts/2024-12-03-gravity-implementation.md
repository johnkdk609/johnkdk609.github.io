---
layout: post
title: 중력(gravity) 구현
categories: Algorithm
date: 2024-12-03 11:10:00 +0900
---
시뮬레이션 유형의 알고리즘 문제를 풀다 보면 <b>중력(gravity)</b>을 구현해야 하는 경우가 많다.

구현 코드는 다음과 같다.

```python
def gravity(arr):
    for c in range(M):
        pointer = 0
        for r in reversed(range(N)):
            if arr[r][c] == 0:
                pointer += 1
            else:
                tmp = arr[r][c]
                arr[r][c] = 0
                arr[r + pointer][c] = tmp

    return arr


# 0은 빈 칸, 1은 떨어져야 하는 물체
grid = [
    [0, 1, 0, 1, 0, 0, 1, 0],
    [1, 1, 0, 1, 0, 1, 1, 0],
    [1, 0, 1, 0, 0, 0, 1, 1],
    [1, 0, 1, 0, 1, 1, 1, 1],
    [0, 1, 1, 1, 0, 0, 0, 1],
    [0, 1, 0, 0, 1, 0, 1, 1],
]

N, M = 6, 8     # 세로, 가로 길이

grid = gravity(grid)

print(*grid, sep='\n')
```

열 우선 순회, 행 반대 순회를 하면서 한 열의 아래쪽부터 본다. 그리고 ```pointer``` 라는 변수를 매 열마다 0 으로 초기화 한다. 그리고 빈 칸이 나올 때마다 ```pointer``` 를 1 증가시킨다. 이는 '떨어져야 하는 물체'가 현재 위치에서 ```pointer``` 만큼 아래로 떨어져야 함을 의미한다.

빈 칸이 아닌, '떨어져야 하는 물체'가 나오면 ```pointer``` 는 그대로 두고, 해당 좌표의 값을 ```tmp``` 에 담는다. (1로 표현했지만, 문제의 경우 다양한 문자, 숫자가 들어갈 수 있다.) 그리고 일단 해당 좌표의 값을 0 으로 변경한다. 마지막으로 ```arr[r + pointer][c] = tmp``` 를 하여 떨어져야 할 물체를 ```pointer``` 만큼 아래로 내린다.

위와 같이 ```pointer``` 이라는 변수를 사용하면 매우 간결하게 중력을 구현할 수 있다. N × M 만큼만 순회하면 된다는 점에서 오버헤드(overhead)가 따로 발생하지 않는다는 것이 장점이다.