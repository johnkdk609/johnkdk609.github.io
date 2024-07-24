---
layout: post
title: TypeError&#58; 'XXX' object is not callable 오류
categories: Python
description: 백준 4963. 섬의 개수
date: 2024-07-24 21:16:00 +0900
---
파이썬으로 알고리즘 문제를 풀다 보면 다음과 같은 에러 메세지에 마주할 때가 있다.

<img src="https://github.com/user-attachments/assets/72925b81-f11f-4b0f-a22d-84b39c6667ab" width="300px" />

이 경우에는 'list' object is not callable 이라고 한다.

<br>

가령 BFS 알고리즘 문제인 '백준 4963. 섬의 개수' 문제를 보자.

내 처음 답안은 다음과 같다.

```python
from collections import deque

# delta 배열 - 상 하 좌 우 좌상 좌하 우상 우하
dr = [-1, 1, 0, 0, -1, 1, -1, 1]
dc = [0, 0, -1, 1, -1, -1, 1, 1]

while True:
    w, h = map(int, input().split())    # w는 열, h는 행
    if w == 0 and h == 0:   # 마지막 입력 받으면 반복문 종료하는 것
        break
    else:
        map = []     # 지도 정보 담을 배열
        for r in range(h):
            map.append(list(map(int, input().split())))

        count = 0       # 섬의 개수 담을 배열
        visited = [[False for _ in range(w)] for _ in range(h)]     # 방문 체크를 위해 boolean 타입으로 2차원 배열 생성

        for r in range(h):  # 행 순회
            for c in range(w):  # 열 순회
                if map[r][c] == 1 and not visited[r][c]:     # 만약 지도에 해당 지점에 Land 이고 방문한 적이 없다면
                    count += 1     # 섬의 개수 + 1
                    visited[r][c] = True    # 해당 지점 방문 체크
                    queue = deque()
                    queue.append((r, c))

                    while queue:
                        x, y = queue.popleft()
                        for d in range(8):  # delta 배열 활용
                            nr = x + dr[d]
                            nc = y + dc[d]
                            if 0 <= nr < h and 0 <= nc < w and not visited[nr][nc] and region[nr][nc] == 1:     # 경계조건 벗어나지 않고, 방문한 적 없으며, 해당 지역 Land 일 때
                                queue.append((nr, nc))  # queue에 추가
                                visited[nr][nc] = True  # 방문 체크

    print(count)
```

<br>

위 코드에 다음과 같은 입력값을 넣었다.

```
5 4
1 0 1 0 0
1 0 0 0 0
1 0 1 0 1
1 0 0 1 0
0 0
```

원래는 위 입력값에 대해 3 이 출력되어야 하는데, 다음과 같은 에러 메세지가 나타나는 것이다.

<img src="https://github.com/user-attachments/assets/0010ab36-ce09-4b99-8474-3ab2b34cee69" width="1000px" />

<br>

해당 에러는 내가 callable 하지 않은 객체(object)를 호출하려고 했기 때문에 발생한다.

'callable object'는 ```__call__``` 을 implements 하는 파이썬의 함수나 클래스이다.

이러한 오류의 이유는 간단하다. 

## 예약어를 변수로 사용했기 때문이다.

<br>

위 코드의 경우, 지도 정보를 받기 위해 'map'이라는 변수를 사용했다.

그런데 파이썬에는 이미 map() 이라는 예약어가 있다. 그래서 변수명으로 'map'을 쓰면 안 된다.

수정한 코드는 다음과 같다. 지도 정보를 받기 위해 변수명을 'region'으로 하였다.

```python
from collections import deque

# delta 배열 - 상 하 좌 우 좌상 좌하 우상 우하
dr = [-1, 1, 0, 0, -1, 1, -1, 1]
dc = [0, 0, -1, 1, -1, -1, 1, 1]

while True:
    w, h = map(int, input().split())    # w는 열, h는 행
    if w == 0 and h == 0:   # 마지막 입력 받으면 반복문 종료하는 것
        break
    else:
        region = []     # 지도 정보 담을 배열 ('region'으로 재 명명)
        for r in range(h):
            region.append(list(map(int, input().split())))

        count = 0       # 섬의 개수 담을 배열
        visited = [[False for _ in range(w)] for _ in range(h)]     # 방문 체크를 위해 boolean 타입으로 2차원 배열 생성

        for r in range(h):  # 행 순회
            for c in range(w):  # 열 순회
                if region[r][c] == 1 and not visited[r][c]:     # 만약 지도에 해당 지점에 Land 이고 방문한 적이 없다면
                    count += 1     # 섬의 개수 + 1
                    visited[r][c] = True    # 해당 지점 방문 체크
                    queue = deque()
                    queue.append((r, c))

                    while queue:
                        x, y = queue.popleft()
                        for d in range(8):  # delta 배열 활용
                            nr = x + dr[d]
                            nc = y + dc[d]
                            if 0 <= nr < h and 0 <= nc < w and not visited[nr][nc] and region[nr][nc] == 1:     # 경계조건 벗어나지 않고, 방문한 적 없으며, 해당 지역 Land 일 때
                                queue.append((nr, nc))  # queue에 추가
                                visited[nr][nc] = True  # 방문 체크

    print(count)
```

이제 실행하면 정상적으로 돌아간다.

<br>

조금 더 구체적으로 해당 에러에 대해 알아보겠다.

(동일하게 리스트 객체를 호출하는 예시에서) 해당 에러는 주로 다음과 같은 상황에서 발생할 수 있다. 

* <b>리스트 이름과 함수 이름이 겹칠 때</b> : 예를 들어, 리스트 이름이 'list'로 정의되어 있을 때, 내장 함수인 'list()'를 호출하려고 하면 충돌이 발생한다.

```python
list = [1, 2, 3]
list()  # TypeError: 'list' object is not callable
```

* <b>잘못된 인덱싱</b> : 리스트에 접근할 때 괄호 대신 소괄호를 사용할 경우 발생할 수 있다.

```python
my_list = [1, 2, 3]
item = my_list(1)  # TypeError: 'list' object is not callable
# 올바른 방법
item = my_list[1]
```

* <b>리스트 객체와 함수 객체를 혼동할 때</b> : 리스트를 함수처럼 사용하려는 경우이다.

```python
my_list = [1, 2, 3]
result = my_list()  # TypeError: 'list' object is not callable
```

<br>

해결 방법은 다음과 같다.

* <b>이름 충돌 방지</b> : 리스트 객체의 이름을 'list'와 같이 내장 함수와 동일하게 정의하지 않는다.

```python
my_list = [1, 2, 3]
new_list = list((4, 5, 6))  # list() 함수 올바르게 사용한 것
```

* <b>올바른 인덱싱 사용</b> : 리스트 요소에 접근할 때는 반드시 대괄호('[]')를 사용한다.

```python
item = my_list[1]
```

* <b>리스트와 함수 구분</b> : 리스트는 인덱싱을 통해 접근하고, 함수는 호출을 통해 사용한다.

```python
def my_function():
    return [1, 2, 3]

result = my_function()  # 올바른 함수 호출
```

<br>

위와 같은 원인과 해결 방법을 통해 'TypeError: 'list' object is not callable' 에러를 방지할 수 있다.