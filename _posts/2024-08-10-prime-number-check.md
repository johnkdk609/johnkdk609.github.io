---
layout: post
title: 소수 (Prime Number) 판별 알고리즘
categories: Algorithm
date: 2024-08-10 23:51:00 +0900
---
어떤 수가 소수(Prime Number)인지 알아내려면 어떻게 해야 할까?

소수란 "1과 자기 자신만을 약수로 가지는 수"이다.

가령 16 이라는 수를 생각해보자. 16이 소수인지 아닌지 판별하기 위해서는 2 부터 15 까지 순서대로 순회를 돌면서 그 수로 16을 나눈 나머지가 0이 되는지 체크해볼 수 있다.

어떤 N 이라는 수가 주어졌을 때, 그 수가 소수인지 아닌지 판별하려면 2 부터 N - 1 까지 순회를 돌면서 체크를 하면 되는 것이다.

그런데 만약 여러 개의 수(ex. M 개)들에 대해 어떤 수가 소수인지 묻는 문제가 나온다면, 시간 복잡도는 O(M x N) 이 된다. 만약 M 과 N 이 매우 큰 수라고 하면 이렇게 접근하면 시간 초과가 나기 쉽다.

<br>

## 해결방안

답안 코드는 다음과 같다.

```python
import math     # 제곱근을 사용하기 위해 라이브러리 사용

def isprime(n):
    if n == 1:  # 1은 그냥 배제
        return False
    else:
        for i in range(2, int(math.sqrt(n)) + 1):   # 딱 2부터 제곱근까지만 순회를 돌린다. Ex) 16은 2 x 8 이기도 하면서 8 x 2 이기도 하다. 딱 그 수의 제곱근까지만 체크하면 된다.
            if n % i == 0:  # 나누어 떨어진다면 그것은 소수가 아니라는 것
                return False
        return True     # 위의 조건에 하나도 안 걸렸다면 소수라는 것


N = int(input())
nums = list(map(int, input().split()))
cnt = 0

for num in nums:
    if isprime(num):
        cnt += 1

print(cnt)
```