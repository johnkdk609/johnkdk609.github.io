---
layout: post
title: "구현 예제 4-1"
date: 2023-03-24 11:46:00 +0900
categories: algorithm
description: "[이것이 취업을 위한 코딩 테스트다] p. 110 예제 4-1"
tags:
  - implementation
  - algorithm
  - direction
---
직접 푼 코드는 이러하다.

```python
n = int(input())
data = list(map(str, input().split()))

def move(dir):
    location = [0, 0]
    
    if dir == "U":
        location[0] -= 1
    if dir == "D":
        location[0] += 1
    if dir == "L":
        location[1] -= 1
    if dir == "R":
        location[1] += 1

    return location

x, y = [1, 1]

for dir in data:
    x += move(dir)[0]
    y += move(dir)[1]

    if x < 1:
        x = 1
    if x > n:
        x = n
    if y < 1:
        y = 1
    if y > n:
        y= n

print(x, y)
```

우선 "U", "D", "L", "R" 중 어떤 것이 나올 때 움직이는 함수 move를 만들었다. 그리고 반복문을 사용해 최종적으로 위치를 알아냈다. 그런데 원래는 continue를 사용해서 범위 밖으로 나갔을 때를 배제하려고 했는데, 그게 잘 안 됐었다.

답안지를 보니

```python
n = int(input())
x, y = 1, 1
plans = input().split()

dx = [0, 0, -1, 1]
dy = [-1, 1, 0, 0]
move_types = ['L', 'R', 'U', 'D']

for plan in plans:
    for i in range(len(move_types)):
        if plan == move_types[i]:
            nx = x + dx[i]
            ny = y + dy[i]
        
        if nx < 1 or ny < 1 or nx > n or ny > n:
            continue

        x, y = nx, ny
```

이런 식으로 상대적으로 간결하게 되어 있었다. 원래 내 답안에서 continue 를 사용하려면, 추가로 nx, ny의 변수를 설정해주면 되겠다는 것을 알았다.

최종적으로 조금 더 간결해진 내 답안은,

```python
n = int(input())
data = list(map(str, input().split()))

def move(dir):
    location = [0, 0]
    
    if dir == "U":
        location[0] -= 1
    if dir == "D":
        location[0] += 1
    if dir == "L":
        location[1] -= 1
    if dir == "R":
        location[1] += 1

    return location

x, y = [1, 1]

for dir in data:
    nx = x + move(dir)[0]
    ny = y + move(dir)[1]

    if nx < 1 or ny < 1 or nx > n or ny > n:
        continue

    x, y = nx, ny
```

이러하다. 답안지에서처럼 함수를 따로 만들지 않고 바로 리스트로 움직이는 정도를 설정하는 방법도 고려해봐야겠다.
