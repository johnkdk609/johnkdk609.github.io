---
layout: post
title: 중복순열, 순열, 중복조합, 조합 구현
categories: Algorithm
date: 2024-08-07 21:35:00 +0900
---
중복순열, 중복조합, 순열, 조합을 코드로 구현할 수 있어야 한다.

주사위를 던지는 문제를 풀어보겠다. 일단 입력값으로 주사위를 던진 횟수 N을 받는다.

## 중복순열 (Permutation with Repetition)

<b>Q. 주사위를 N번 던져서 나올 수 있는 모든 경우를 출력하시오. (작은 숫자부터 출력한다.)</b>

N = 3 일 때, 출력값은 다음과 같다.

```
1 1 1
1 1 2
1 1 3
1 1 4
1 1 5
1 1 6
1 2 1
1 2 2
...
6 6 4
6 6 5
6 6 6
```

<br>

이 경우는 중복순열(Permutation with Repetition)에 해당한다. 백트래킹으로 풀 수 있고, 답안 코드는 다음과 같다.

```python
def dfs(n, alst):    # 중복순열
    if n == N:
        print(*alst)
        return
    for j in range(1, 7):
        dfs(n + 1, alst + [j])

N = int(input())
dfs(0, [])
```

주사위에는 1부터 6까지의 숫자가 있기 때문에 range(1, 7) 로 설정하였다.

## 중복조합 (Combination with Repetition)

<b>Q. 주사위를 N번 던져서 중복이 되는 경우를 제외하고 나올 수 있는 모든 경우를 출력하시오. (작은 숫자부터 출력한다.)</b>

N = 3 일 때, 출력값은 다음과 같다.

```
1 1 1
1 1 2
1 1 3
1 1 4
1 1 5
1 1 6
1 2 2
1 2 3
...
5 5 6
5 6 6
6 6 6
```

<br>

이 경우는 중복조합(Combination with Repetition)에 해당한다. 백트래킹을 활용해 구현할 수 있고, 답안 코드는 다음과 같다.

```python
def dfs(n, start, alst):
    if n == N:
        print(*alst)
        return

    for j in range(start, 7):
        dfs(n + 1, j, alst + [j])   # j + 1 이 아닌 j 로 설정하고 재귀 호출을 하여 중복이 가능하게 하였다.
```

위 코드를 보면, 인자로 start를 넣었는데, 이는 조합을 구현하기 위함이다. 그리고 재귀 호출을 할 때 인자에 j + 1 이 아닌 j 를 넣음으로써 중복이 가능하게 하였다.

이 중복조합 코드를 다른 방식으로 짠다면 다음과 같이 할 수 있다.

```python
def dfs(n, alst):    # 중복조합
    if n == N:
        print(*alst)
        return
    for j in range(1, 7):
        if len(alst) == 0:
            dfs(n + 1, alst + [j])
        elif len(alst) != 0 and j >= alst[-1]:
            dfs(n + 1, alst + [j])
```

alst의 길이가 0 즉 안에 아무것도 들어있지 않다면 그냥 재귀 호출을 하고, alst의 길이가 1 이상 즉 안에 무언가 들어있다면 마지막에 들어간 요소 이상인 것들로 재귀 호출을 하는 것이다. 조금 더 직관적인 코드라고 할 수 있겠다.

## 순열 (Permutation without Repetition)

<b>Q. 주사위를 N번 던져서 중복이 되는 경우를 제외하고 나올 수 있는 모든 경우를 출력하시오. (작은 숫자부터 출력한다.)</b>

N = 3 일 때, 출력값은 다음과 같다.

```
1 2 3
1 2 4
1 2 5
1 2 6
1 3 2
1 3 4
...
6 5 3
6 5 4
```

<br>

이 경우는 순열(Permutation)에 해당한다. 백트래킹을 활용해 구현할 수 있다. 답안 코드는 다음과 같다.

```python
def dfs(n, alst):    # 순열
    if n == N:
        print(*alst)
        return
    for j in range(1, 7):
        if visited[j] == 0:
            visited[j] = 1
            dfs(n + 1, alst + [j])
            visited[j] = 0

N = int(input())
visited = [0] * 7

dfs(0, [])
```

중복 방지를 위해 visited 배열을 사용했다. 아직 포함되지 않았다면, 포함(방문체크)을 시키고 dfs() 메서드를 호출한 다음, 다시 포함 여부를 제거해야 한다. 그래야 visited 배열을 다시 활용할 수 있다.

## 조합 (Combination without Repetition)

<b>Q. 주사위를 N번 던져서 나올 수 있는 모든 경우를 출력하시오. 나오는 순서는 중요하지 않다. (작은 숫자부터 출력한다.)</b>

N = 3 일 때, 출력값은 다음과 같다.

```
1 2 3
1 2 4
1 2 5
1 2 6
1 3 4
1 3 5
...
3 4 6
3 5 6
4 5 6
```

<br>

이 경우는 조합(Combination)에 해당한다. 마찬가지로 백트래킹을 활용하여 구현할 수 있다. 답안 코드는 다음과 같다.

```python
def dfs(n, start, alst):    # 조합
    if n == N:
        print(*alst)
        return

    for j in range(start, 7):
        dfs(n + 1, j + 1, alst + [j])   # 중복 방지를 위해 j + 1 로 설정했다.

N = int(input())
dfs(0, 1, [])
```

중복 방지를 위해 dfs() 메서드를 다시 호출할 때 start 인자 값을 j + 1 로 두었다.

이 조합 코드를 또 다른 방식으로 작성하자면 다음과 같이 할 수 있다.

```python
def dfs(n, alst):    # 조합
    if n == N:
        print(*alst)
        return
    for j in range(1, 7):
        if len(alst) == 0:  # alst의 마지막 인자와 비교하면서 값을 넣을 것이기 때문에, 만약 alst의 길이가 0으로 아직 아무 값도 안 들어있다면
            dfs(n + 1, alst + [j])  # dfs() 메서드 다시 호출
        elif j > alst[-1]:  # alst의 길이가 1 이상이고, j가 마지막 값보다 커서 중복이 안 되게 함
            dfs(n + 1, alst + [j])
```

조금 더 직관적인 코드라고 할 수 있겠다.