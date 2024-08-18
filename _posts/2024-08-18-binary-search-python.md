---
layout: post
title: [파이썬] 이진 탐색
categories: Algorithm
description: 백준 1920 수 찾기
date: 2024-08-19 00:00 +0900
---
이진 탐색 (binary search)는 정렬된 배열이나 리스트에서 특정 값을 효율적으로 찾기 위한 알고리즘이다. 이진 탐색은 값을 찾을 때마다 탐색 범위를 절반으로 줄여 나가기 때문에, <b>시간 복잡도가 O(log N) 으로 매우 효율적</b>이다.

그렇기 때문에 N의 크기가 매우 클 때 유용한 방법으로 쓸 수 있다.

<a href="https://www.acmicpc.net/problem/1920">백준 1920번 수 찾기</a> 문제는 이진 탐색으로 해결해야 하는 문제이다.

답안 코드는 다음과 같다.

```python
def binarysearch(arr, n):    # 이진탐색 방법으로 접근 - 시간 초과 방지를 위하여
    start = 0
    end = len(arr) - 1
    while start <= end:
        m = (start + end) // 2
        if arr[m] == n:
            return True
        elif arr[m] < n:
            start = m + 1
        else:
            end = m - 1
    return False


N = int(input())
Alst = list(map(int, input().split()))
Alst.sort()    # 이진 탐색을 하기 위해서는 무조건 정렬을 해야 한다.
M = int(input())
Mlst = list(map(int, input().split()))

for i in range(M):
    if binarysearch(Alst, Mlst[i]):
        print(1)
    else:
        print(0)
```

이진 탐색을 하기 위해서는 전제 조건이 '정렬되어 있어야 한다는 것'이다. 그래서 위 코드에서 ```Alst.sort()```를 입력하여 정렬을 먼저 수행하였다.

우선 초기화를 하는데, 리스트의 시작 위치를 start로, 끝 위치를 end 로 설정한다. 초기에는 start 는 리스트의 첫 번째 인덱스(0)를, end 는 리스트의 마지막 인덱스를 가리킨다.

그리고 start &#60;&#61; end 인 동안 즉, start 가 end 보다 작거나 같으면 계속 반복한다. 이때 중요한 것은 m 으로, 중간 위치이고 ```(start + end) // 2``` 로 구할 수 있다.

그리고 중간 위치 m 에 있는 값이 찾고자 하는 값 n 과 같다면 종료한다. 위 코드에서는 True를 반환하였다.

만약 m 에 있는 값이 n 보다 작으면, 찾고자 하는 값이 오른쪽에 있다는 뜻이므로 start 를 m + 1 로 갱신한다.

반대로, 중간 위치 m 의 값이 n 보다 크다면, 찾고자 하는 값이 왼쪽에 있다는 뜻이므로 end 를 m - 1 로 갱신한다.

최종적으로 start &#60;&#61; end 의 조건이 끝나고 반복문이 종료되었을 때까지 값을 찾지 못했다면, 이는 리스트 내에 해당 값이 없다는 뜻이므로 False를 반환한다.

<br>

이진 탐색은 정렬된 배열에서 매우 효율적으로 값을 찾을 수 있는 방법이다. 배열의 중간값을 기준으로 탐색 범위를 절반씩 줄여나가면서 찾고자 하는 값을 검색한다. 이로 인해 배열이 클수록 그 효율성은 더욱 두드러진다.