---
layout: post
title: 버블 정렬 (Bubble Sort)
categories: Algorithm
date: 2025-01-17 23:48:00 +0900
---
버블 정렬(Bubble Sort)을 코드로 구현하면 다음과 같다.

```python
def bubblesort(lst):
    for i in range(len(lst) - 1):
        swapped = False
        for j in range(len(lst) - i - 1):
            if lst[j] > lst[j + 1]:
                lst[j], lst[j + 1] = lst[j + 1], lst[j]
                swapped = True

        if not swapped:
            break
    return lst


data = [9, 7, 3, 5, 10, 4, 1]
print(bubblesort(data))

# 출력 결과 : [1, 3, 4, 5, 7, 9, 10]
```

<br>

버블 정렬의 패턴을 정리하면 다음과 같다.

* <b>n 개의 원소가 있는 리스트가 있는 경우 최대 n - 1 번의 로직을 적용한다.</b>
* <b>로직을 1번 적용할 때마다 가장 큰 숫자가 뒤에서부터 1개씩 결정된다.</b>
* <b>로직이 경우에 따라 일찍 끝날 수 있다. 따라서 로직을 적용할 때 한 번도 데이터가 교환된 적이 없다면 이미 정렬된 상태이므로 더 이상 로직을 반복 적용할 필요가 없다.</b>

<br>

버블 정렬의 시간 복잡도는 <b>O(N²)</b> 이다. 반복문이 두 개이기 때문이다. 완전 정렬이 되어 있는 상태라면 최선은 O(N) 이다.