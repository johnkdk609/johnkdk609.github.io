---
layout: post
title: 퀵 정렬(Quick Sort) & 병합 정렬(Merge Sort)
categories: Algorithm
description: 백준 2750번 수 정렬하기
date: 2024-08-12 23:16:00 +0900
---
파이썬으로 퀵 정렬과 병합 정렬을 구현할 수 있어야 한다.

<a href="https://www.acmicpc.net/problem/2750">백준 2750번 수 정렬하기</a> 문제에 대해 각각 퀵 정렬, 병합 정렬로 접근하였다.

<br>

## Quick Sort

답안 코드는 다음과 같다. 템플릿으로 여겨도 되겠다.

```python
def quicksort(lst):    # 재귀적인 코드
    if len(lst) <= 1:
        return lst
    left = []
    right = []
    pivot = lst.pop(0)    # 피벗값을 맨 앞 값으로 하였다.

    for n in lst:
        if n < pivot:
            left.append(n)
        else:
            right.append(n)

    return quicksort(left) + [pivot] + quicksort(right)    # 재귀 호출


N = int(input())
nums = []
for _ in range(N):
    nums.append(int(input()))

alst = quicksort(nums)
print(*alst, sep='\n')
```

<br>

## Merge Sort

병합 정렬을 사용한 코드이다.

```python
def mergesort(lst):    # 병합정렬
    if len(lst) <= 1:    # 종료조건
        return lst

    m = len(lst) // 2    # 절반으로 나누기 위함
    left = mergesort(lst[:m])    # 재귀 호출
    right = mergesort(lst[m:])    # 재귀 호출

    alst = []
    while left and right:    # 둘 중 하나가 전부 빌 때까지 진행
        if left[0] < right[0]:
            alst.append(left.pop(0))
        else:
            alst.append(right.pop(0))

    return alst + left + right    # 어차피 left, right 중 하나는 빈 리스트이다.


N = int(input())
nums = []
for _ in range(N):
    nums.append(int(input()))

nums_sort = mergesort(nums)
print(*nums_sort, sep='\n')
```

마찬가지로 위 코드를 템플릿으로 여기면 되겠다.

<br>

그런데 병합 정렬을 구현할 때, 위와 같이 ```.pop(0)``` 의 방식을 사용하면 시간이 매우 오래 걸릴 수 있다. 경우에 따라서는 시간 초과가 발생할 수도 있다.

이때 코드를 약간 수정해 인덱스로 접근하는 방식을 사용하면 시간을 획기적으로 줄일 수 있다.

인덱스를 사용한 코드는 다음과 같다.

```python
def mergesort(lst):     # 병합 정렬
    global cnt
    if len(lst) <= 1:        # 종료 조건
        return lst
 
    m = len(lst) // 2       
    left = mergesort(lst[:m])
    right = mergesort(lst[m:])
    if left[-1] > right[-1]:
        cnt += 1
 
    alst = []
    l = r = 0   # .pop(0) 의 방법을 사용했을 때 계속 시간 초과가 발생했다. 이렇게 인덱스로 접근하는 것이 훨씬 더 빠르다.
    while l < len(left) and r < len(right):
        if left[l] < right[r]:
            alst.append(left[l])
            l += 1
        else:
            alst.append(right[r])
            r += 1
 
    return alst + left[l:] + right[r:]  # alst 그리고 left의 남아있는 것, right의 남아있는 것을 합친다. 이때 left와 right 중 하나는 비어있는 리스트이다.
 
 
T = int(input())
for tc in range(1, T + 1):
    N = int(input())
    A = list(map(int, input().split()))
 
    cnt = 0
 
    Asort = mergesort(A)
    print(f'#{tc} {Asort[N // 2]} {cnt}')
```

<br>

## 퀵 정렬과 병합 정렬 비교

퀵 정렬은 시간 복잡도가 평균적으로 O(N log N) 이다. 그런데 최악의 경우 O(N ^ 2) 가 된다.

반면 병합 정렬은 최악의 경우 시간 복잡도가 O(N log N) 이다. (그런 차원에서 병합 정렬을 외우고 있는 것이 더 좋을 수도 있겠다.)

그런데 파이썬의 내장 함수인 ```sort()```의 경우 이렇게 직접 퀵 정렬, 병합 정렬을 구현하여 사용하는 것보다 속도가 빠르다. 

그러므로 웬만해서는 sort() 함수를 사용하면 된다.