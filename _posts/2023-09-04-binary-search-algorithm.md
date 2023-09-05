---
layout: post
title: 이진 탐색 (Binary Search) 알고리즘
description: 이것이 취업을 위한 코딩 테스트다 with 파이썬
categories: Algorithm
date: 2023-09-04 16:47:00 +0900
---
이진 탐색(Binary Search)이란 반으로 쪼개면서 탐색하는 것이다.

이진 탐색 알고리즘은 배열 내부의 데이터가 정렬되어 있어야만 사용할 수 있다. 탐색 범위를 절반씩 좁혀가며 데이터를 탐색하는 특징이 있다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2e437679-b0fd-40e8-b4cb-6c2593fba3bb)

이진 탐색은 위치를 나타내는 변수 3개를 사용한다. 탐색하고자 하는 범위의 시작점, 끝점, 그리고 중간점이다. 찾으려는 데이터와 중간점(Middle) 위치에 있는 데이터를 반복적으로 비교해서 원하는 데이터를 찾는 것이 이진 탐색 과정이다.

이진 탐색을 구현하는 방법에는 2가지가 있다. 하나는 재귀 함수를 이용하는 방법이고, 다른 하나는 반복문을 이용하는 방법이다.


## 재귀 함수를 이용한 이진 탐색 코드

```python
def binary_search(array, target, start, end):
    if start > end:
        return None
    mid = (start + end) // 2
    if array[mid] == target:
        return mid
    elif array[mid] > target:
        return binary_search(array, target, start, mid - 1)
    else:
        return binary_search(array, target, mid + 1, end)
    
n, target = list(map(int, input().split()))
array = list(map(int, input().split()))

result = binary_search(array, target, 0, n - 1)
if result == None:
    print("원소가 존재하지 않습니다.")
else:
    print(result + 1)
```


## 반복문을 이용한 이진 탐색 코드

```python
def binary_search(array, target, start, end):
    while start <= end:
        mid = (start + end) // 2
        if array[mid] == target:
            return mid
        elif array[mid] > target:
            end = mid - 1
        else:
            start = mid + 1
    return None

n, target = list(map(int, input().split()))
array = list(map(int, input().split()))

result = binary_search(array, target, 0, n - 1)
if result == None:
    print("원소가 존재하지 않습니다.")
else:
    print(result + 1)
```


## 이진 탐색 알고리즘의 시간 복잡도

이진 탐색 알고리즘의 시간 복잡도는 <b>O(log N)</b>이다.

한 단계를 거칠 때마다 확인하는 원소가 평균적으로 절반으로 줄어든다. 예를 들어 데이터의 개수가 32개일 때, 1단계만 거치면 이상적인 경우 16개 가량의 데이터만 남게 될 것이다. 2단계만 거치면 8개 가량의 데이터만 확인하면 될 것이다. 즉, 단계마다 2로 나누는 것과 동일하므로 연산 횟수는 log₂N에 비례한다고 할 수 있다.

이러한 이유로 코딩 테스트의 이진 탐색 문제는 탐색 범위가 큰 상황에서의 탐색을 가정하는 문제가 많다. 탐색 범위가 2,000만을 넘어가면 이진 탐색으로 문제에 접근해보는 것도 생각해봐야 한다. 처리해야 할 데이터의 개수나 값이 1,000만 단위 이상으로 넘어가면 이진 탐색과 같이 O(log N)의 속도를 내야 하는 알고리즘을 떠올려야 문제를 풀 수 있는 경우가 많다는 점을 기억해야 한다.