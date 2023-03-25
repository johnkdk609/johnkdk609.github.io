---
layout: post
title: 구현 실전 문제 3. 게임 개발
categories: algorithm
tag:
  - Implementation
  - python list comprehension
data: 2023-03-23 11:16:00 +09:00
description: \<이것이 취업을 위한 코딩 테스트다\> p.118 실전 문제 3
---
이 문제에서 캐릭터가 한 번 다녀간 장소를 어떻게 저장해둘지 고민을 했는데, 처음에는 집합을 이용해서 삽입을 하려 했다.

```python
visited = set()

if (nx, ny) not in visited:
    visited.add((nx, ny))
```

하지만, 한 번 방문한 장소를 체크해두는 것보다도 잘 안 풀리는 것이 있었으니, 이것은 북, 서, 남, 동, 북, ... 방향으로 돌 때, 네 번 다 돌고 사방이 1이거나 이미 가본 곳일 때 한 스텝 돌아가는 것이었다.
while문으로 돌리면서 적정 타이밍에 반복문을 끝내야 하는데 이 타이밍도 어떻게 해야 할지 잘 모르겠어서 답안을 보았다.

답안은 이러하다.

```python
# N, M을 공백으로 구분하여 입력 받기
n, m = map(int, input().split())

# 방문한 위치를 저장하기 위한 맵을 생성하여 0으로 초기화
d = [[0] * m for _ in range(n)]
# 현재 캐릭터의 X 좌표, Y 좌표, 방향을 입력 받기
x, y, direction = map(int, input().split())
d[x][y] = 1 # 현재 좌표 방문 처리

# 전체 맵 정보를 입력 받기
array = []
for i in range(n):
    array.append(list(map(int, input().split())))

# 북, 동, 남, 서 방향 정의
dx = [-1, 0, 1, 0]
dy = [0, 1, 0, -1]

# 왼쪽으로 회전
def turn_left():
    global direction
    direction -= 1
    if direction == -1:
        direction = 3

# 시뮬레이션 시작
count = 1
turn_time = 0
while True:
    # 왼쪽으로 회전
    turn_left()
    nx = x + dx[direction]
    ny = y + dy[direction]
    # 회전한 이후 정면에 가보지 않은 칸이 존재하는 경우 이동
    if d[nx][ny] == 0 and array[nx][ny] == 0:
        d[nx][ny] = 1
        x = nx
        y = ny
        count += 1
        turn_time = 0
        continue
    # 회전한 이후 정면에 가보지 않은 칸이 없거나 바다인 경우
    else:
        turn_time += 1
    # 네 방향 모두 갈 수 없는 경우
    if turn_time == 4:
        nx = x - dx[direction]
        ny = y - dy[direction]
        # 뒤로 갈 수 있다면 이동하기
        if array[nx][ny] == 0:
            x = nx
            y = ny
        # 뒤가 바다로 막혀있는 경우
        else:
            break
        turn_time = 0

# 정답 출력
print(count)
```

위 코드를 보면, 일단 2차원 리스트를 파이썬 리스트 컴프리헨션 문법을 사용해 초기화했다. 파이썬에서는 2차원 리스트를 선언할 때 리스트 컴프리헨션을 이용하는 것이 효율적이라는 것을 기억해야겠다.

그리고 왼쪽으로 회전하는 함수 turn_left()에서 global 키워드를 사용했는데, 이는 정수형 변수인 direction 변수가 함수 바깥에서 선언된 전역변수이기 때문이다.

turn_time이라는 변수는 왼쪽으로 회전을 몇 번 했는지를 체크하는 것이다. 이 변수를 통해, turn_time == 4 일 경우 즉, 네 방향 모두 갈 수 없는 경우를 컨트롤하는 것이다. 위 코드에서는 nx = x - dx[direction]의 방법으로 돌아가는 것을 하였다.

조금만 난이도가 높아져도 풀기 쉽지 않다.
