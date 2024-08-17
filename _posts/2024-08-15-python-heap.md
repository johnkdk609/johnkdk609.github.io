---
layout: post
title: 파이썬에서 힙(Heap) 사용
categories: Python
date: 2024-08-15 23:54:00 +0900
---
파이썬에서 힙 (Heap) 자료구조를 사용하는 방법은 다음과 같다.

가령 다음 코드는 <a href="https://www.acmicpc.net/problem/1927">백준 1927번 최소 힙</a> 문제에 대한 답안 코드이다.

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

그래서 반대로 최댓값이 나오는 최대 힙을 구현하고 싶다면, heapq 를 임포트하고 heappush를 할 때 -1 을 곱한 값들을 넣으면 된다. 그렇게 되면 일단 실제로 가장 큰 값이 가장 작은 값이 될 것이고, heappop으로 최솟값(실제로는 가장 큰 값)을 뺀 다음 다시 -1 을 곱해주면 되는 것이다.

파이썬에서는 이렇게 쉽게 최소 힙, 최대 힙을 구현할 수 있다.

최대 힙을 구현한 문제는 