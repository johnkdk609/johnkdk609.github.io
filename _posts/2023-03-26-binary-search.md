---
layout: post
title: 백준 1920번 - 수 찾기
data: 2023-03-26 16:30:00 +09:00
categories: Algorithm
---
백준 1920번 - 수 찾기 문제를 풀 때에 분할 정복 알고리즘을 사용해야 한다. 그래서 해당 문제에 대한 답으로,

```python
n = int(input())
data_n = list(map(int, input().split()))
m = int(input())
data_m = list(map(int, input().split()))

data_n.sort()

def binary_search(data, search):
    if len(data) == 1 and search == data[0]:
        return 1
    if len(data) == 1 and search != data[0]:
        return 0
    if len(data) == 0:
        return 0
    
    medium = len(data) // 2
    if search == data[medium]:
        return 1
    else:
        if search > data[medium]:
            return binary_search(data[medium:], search)
        else:
            return binary_search(data[:medium], search)
        
for data in data_m:
    print(binary_search(data_n, data))
```

이렇게 작성했다. 그런데 이렇게 하니 백준 사이트에서 계속 시간 초과가 발생했다.

n의 범위는 (1 ≤ N ≤ 100,000), m의 범위는 (1 ≤ M ≤ 100,000)이다.

시간 초과가 발생했던 이유는 다음과 같다.

1. len() 함수를 사용하면 전체 리스트의 길이를 재기 위해 루프가 돌아야 하는데, 이는 큰 리스트의 경우 비효율적이다. 인덱스 위주의 탐색이 더 효율적이다. (인덱스 위주의 탐색이란 start와 end를 사용하여 가운데 인덱스를 계산하는 방법이다.)
2. 재귀적으로 구현하기보다는 반복적으로 구현하는 코드를 통해 효율성을 높여야 한다.
3. 리스트 슬라이싱을 이용해 재귀를 할 때마다 새로운 리스트를 만들기에, 커다란 리스트의 경우 비효율이 발생한다.

이것을 개선한 코드는 다음과 같다.

```python
n = int(input())
data_n = list(map(int, input().split()))
m = int(input())
data_m = list(map(int, input().split()))

data_n.sort()

def binary_search(value, start, end):
    if start > end:
        return False
    
    medium = (start + end) // 2
    if data_n[medium] > value:
        return binary_search(value, start, medium - 1)
    elif data_n[medium] < value:
        return binary_search(value, medium + 1, end)
    else:
        return True
        
for data in data_m:
    if binary_search(data, 0, n - 1):
        print (1)
    else:
        print (0)
```
이 개선된 코드의 경우,

1. 인덱스 위주의 탐색을 사용하여, start와 end를 통해 가운데 인덱스를 계산하였다. 이는 len() 함수를 사용하는 것보다 효율적이다.
2. 이진 탐색을 수행하기 위해 재귀적으로 접근하기 보다는 반복적으로 접근한다.
3. 리스트 슬라이싱을 사용하지 않는다.

의 이유들로, 시간 효율성이 높아졌고 통과할 수 있었다.



