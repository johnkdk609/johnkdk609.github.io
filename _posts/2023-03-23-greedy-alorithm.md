---
layout: post
title: "그리디 알고리즘 - 2. 큰 수의 법칙"
description: "<이것이 취업을 위한 코딩 테스트다> p.92 실전 문제 2"
tags:
  - greedy
  - algorithm
date: 2023-03-23 20:38:00 +0900
author: John Kim
---

이 문제는 그리디 알고리즘 문제이다.

내 답안은,

~~~python
n, m, k = map(int, input().split())
data = list(map(int, input().split()))

def qsort(data):
    if len(data) <= 1:
        return data
    
    pivot = data[0]
    # python list comprehension 사용
    left = [ item for item in data[1:] if pivot > item ]
    right = [ item for item in data[1:] if pivot <=  item ]

    return qsort(left) + [pivot] + qsort(right)

max_data = qsort(data)[-1]
second_data = qsort(data)[-2]

bigNumber = 0
count = 0

for i in range(m):
    if count < k:
        bigNumber += max_data
        count += 1
    else:
        count = 0
        bigNumber += second_data

print(bigNumber)
~~~

이렇게 된다. 파이썬의 내장 함수인 sort를 그냥 쓸 수도 있지만, 이전에 공부했던 quick sort의 코드를 사용했다.

그런데 답안 중에서 흥미로운 것이 있었다. 만약 M의 크기가 100억 이상처럼 커진다면 이러한 코드는 시간 초과 판정을 받을 수 있다는 것이다. 그래서 간단한 수학적 아이디어를 이용해 더 효율적으로 문제를 해결할 수 있다고 한다.

반복되는 수열에 대해 파악하는 것이다. 가령, N이 5이고, 입력값이 2, 4, 5, 4, 6으로 주어졌다면, 가장 큰 수는 6, 두 번째로 큰 수는 5이다.
이때 M이 8이고 K가 3이라면 다음과 같이 더했을 때 합이 최대가 된다. (6 + 6 + 6 + 5) + (6 + 6 + 6) = 46

이 예시에서 수열 {6, 6, 6, 5}가 반복된다. 수열의 길이는 4이고, 이는 (K + 1) 이다. 그리고 수열이 반복되는 횟수는 M을 (K + 1)로 나눈 몫이다. 다시 여기에 K를 곱해주면 가장 큰 수가 등장하는 횟수가 된다.

결론적으로, 코드는 다음과 같다.

~~~python
n, m, k = map(int, input().split())
data = list(map(int, input().split()))

data.sort() # 입력 받은 수 정렬
first = data[-1]  # 가장 큰 수
second = data[-2] # 두 번째로 큰 수

# 가장 큰 수가 더해지는 횟수 계산
count = m // (k + 1) * k + m % (k + 1)

result = count * first + (m - count) * second

print(result)
~~~

이렇게 수학적인 사고력으로 문제를 푸는 것도 고려해봐야겠다.
