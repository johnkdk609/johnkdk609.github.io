---
layout: post
title: 곱하기 혹은 더하기 문제
description: 이것이 취업을 위한 코딩 테스트다 with 파이썬 p.312
categories: Algorithm
date: 2023-11-18 23:31:00 +0900
---
곱하기 혹은 더하기 문제의 경우, 우선 입력받은 수를 각 수로 끊어서 리스트에 넣어야 한다.

일단 처음에 입력받는 수는 int가 아니라 str으로 하였다. 그리고 str을 리스트에 넣어서 문자 단위로 리스트에 들어가게 하고, 반복문을 돌려 각각을 int로 parsing 하였다.

그 다음에 sort를 하여 순서대로 두었다. (사실 이 작업은 필수적이지 않다.)

이제 반복문을 돌면서 0이 아닌 수들은 모두 곱한다. 음수는 존재하지 않으므로, 그냥 0이 아닌 수끼리 곱하면 그게 곧 최댓값이다.

답안 코드는 다음과 같다.

```python
n = input()
str_nums = list(n)
int_nums = []
for i in str_nums:
    int_nums.append(int(i))

int_nums.sort()

result = 1
for i in int_nums:
    if i != 0:
        result *= i

print(result)
```

<br>

입력 예시 1

```
02984
```

출력 예시 1

```
576
```

입력 예시 2

```
567
```

출력 예시 2

```
210
```