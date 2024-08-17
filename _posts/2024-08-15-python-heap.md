---
layout: post
title: 파이썬에서 힙(Heap) 사용
categories: Python
description: 백준 1927 최소 힙, 백준 11279 최대 힙
date: 2024-08-15 23:54:00 +0900
---
힙(Heap)은 이진 트리(binary tree) 기반의 자료 구조로, 주로 <b>우선순위 큐(priority queue)</b>를 구현하는 데에 사용된다.

파이썬의 heapq 모듈은 기본적으로 <b>최소 힙(min-heap)</b>을 구현하며, 이 모듈을 사용하면 리스트를 힙으로 쉽게 변환하거나, 힙에서 요소를 추가하거나 제거하는 작업을 효율적으로 수행할 수 있다.

<br>

## heapq 모듈의 주요 함수

### heapq.heapify(iterable)

heapify 모듈은 주어진 리스트를 최소 힙(min-heap)으로 변환한다.

시간 복잡도는 O(N) 이다. 예시는 다음과 같다.

```python
import heapq

data = [5, 3, 8, 1, 2, 7]
heapq.heapify(data)
print(data)     # [1, 2, 7, 3, 5, 8]
```

이 리스트는 힙 특성을 만족하는 구조로 재배열된다.

<br>

### heapq.heappush(heap, item)

heappush 모듈은 힙에 새로운 요소를 추가한다. 시간 복잡도는 O(log N) 이다.

예시는 다음과 같다.

```python
heapq.heappush(data, 4)
print(data)     # [1, 2, 4, 3, 5, 8, 7]
```

요소 4가 힙에 추가되며 힙 특성이 유지된다.

<br>

### heapq.heappop(heap)

heappop 모듈은 힙에서 가장 작은 요소(루트)를 제거하고 반환한다. 시간 복잡도는 O(log N) 이다.

예시는 다음과 같다.

```python
smallest = heapq.heappop(data)
print(smallest)     # 1
print(data)         # [2, 3, 4, 7, 5, 8]
```

가장 작은 요소인 1이 제거된 후, 힙 특성이 유지된다.

<br>

## heapq 모듈 사용 예시

백준 문제들에서 파이썬 heapq 모듈을 임포트하여 사용한 예시들은 다음과 같다.

다음은 <a href="https://www.acmicpc.net/problem/1927">백준 1927번 최소 힙</a> 문제에 대한 답안 코드이다.

```python
import heapq, sys    # heapq 를 사용하기 위해 라이브러리 임포트.
input = sys.stdin.readline

N = int(input())
hq = []
for _ in range(N):
    x = int(input())
    if x == 0:
        if len(hq) != 0:    # 리스트 길이가 1 이상이면
            print(heapq.heappop(hq))    # 최솟값 출력 및 힙에서 제거
        else:
            print(0)
    else:
        heapq.heappush(hq, x)
```

우선 heapq 라이브러리를 임포트 한다. 그리고 hq 리스트를 생성한다. 그리고 힙에 추가를 해야 할 경우 ```heap.heappush(hq, x)``` 의 방식으로 넣는 것이다.

그리고 힙에서 최솟값을 뺄 때에는 ```heapq.heappop(hq)```를 입력하면 된다.

파이썬에서 heapq 를 임포트해서 힙큐를 사용하면 기본적으로 최소 힙으로 작동한다. heappop을 하면 최솟값이 나오는 것이다.

<br>

반대로 최댓값이 나오는 최대 힙을 구현하고 싶다면, heapq 를 임포트하고 heappush를 할 때 -1 을 곱한 값들을 넣으면 된다. 그렇게 되면 일단 실제로 가장 큰 값이 가장 작은 값이 될 것이고, heappop으로 최솟값(실제로는 가장 큰 값)을 뺀 다음 다시 -1 을 곱해주면 되는 것이다.

파이썬에서는 이렇게 쉽게 최소 힙, 최대 힙을 구현할 수 있다.

최대 힙을 구현한 문제는 <a href="https://www.acmicpc.net/problem/11279">백준 11279번 최대 힙</a> 문제가 있다.

위 문제에 대한 답안 코드는 다음과 같다.

```python
import heapq, sys

input = sys.stdin.readline    # 이거 안 하면 계속 시간 초과가 발생한다.

N = int(input())
hq = []
for _ in range(N):
    x = int(input())
    if x == 0:
        if len(hq) == 0:
            print(0)
        else:
            print(-heapq.heappop(hq))   # 꺼낼 때에는 -1 을 곱한 채로 꺼낸다. (-1을 곱한 채로 넣었기 때문)
    else:
        heapq.heappush(hq, -x)  # 기본적으로 최소 힙으로 작동하기 때문에, -1 을 곱한 값으로 heappush 를 한다.
```

```heapq.heappush(hq, -x)``` 와 같은 방식으로, 아예 heappush를 할 때 -1 을 곱하여 넣는 것이다.

<br>

heapq 라이브러리를 사용할 때, 그냥 ```import heapq```를 하면 heappush, heappop을 할 때마다 ```heapq.heappush(hq, ~)```, ```heapq.heappop(hq)``` 와 같이 앞에 라이브러리명을 매번 붙여줘야 한다.

이때 ```from heapq import heappop, heappush``` 의 방식으로 좀 더 구체적으로 라이브러리를 임포트하면, 그냥 ```heappush(hq, ~)```, ```heappop(hq)``` 와 같이 ```heap.```을 앞에 붙이지 않고 바로 메서드를 사용할 수 있다.

<br>

## 정리

```heapq``` 모듈은 파이썬에서 효율적인 힙 자료구조를 제공하며, 우선순위 큐와 같은 다양한 알고리즘 문제를 쉽게 해결할 수 있도록 도와준다.

heapify 를 사용해 리스트를 힙으로 변환할 수 있고, (heapify 모듈을 사용하지 않더라도) heappush, heappop 등을 통해 힙에서의 삽입과 삭제를 수행할 수 있다.