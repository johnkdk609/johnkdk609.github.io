---
layout: post
title: 위상 정렬 알고리즘 (topology sort algorithm)
categories: Algorithm
date: 2024-11-08 21:42:00 +0900
---
위상 정렬(topology sort)이란 유향 그래프의 정점들을 간선의 방향을 거스르지 않도록 나열하는 정렬 방법이다.

순서가 정해져 있는 작업들을 차례대로 수행해야 할 때, 그 순서를 결정해준다.

위상 정렬을 가장 잘 설명하는 문제는 선수과목(prerequisite) 구조가 있다. 어떤 과목을 수강하기 위해서 선수과목을 수강해야 한다면, 이러한 과목들의 수강 순서를 결정할 때 위상 정렬 알고리즘을 사용하면 되는 것이다.

위상 정렬이 성립하기 위해서는 그래프에 사이클(cycle, 순환)이 존재하면 안 된다.

즉, 그래프가 비순환 유향 그래프(directed acyclic graph)여야 하는 것이다.

이러한 위상 정렬은 deque() 을 이용해서 구현할 수 있다. 위상 정렬 알고리즘을 구현한 코드는 다음과 같다.

```python
from collections import deque


def topology_sort():
    queue = deque()
    result = []
    for i in range(1, V + 1):
        if indegree[i] == 0:
            queue.append(i)

    while queue:
        current = queue.popleft()
        result.append(current)

        for i in graph[current]:
            indegree[i] -= 1
            if indegree[i] == 0:
                queue.append(i)

    if len(result) != V:
        print('cycle')      # 사이클 발생, 위상정렬 불가!
    else:
        print(*result)      # 위상정렬 완료
    return


V, E = map(int, input().split())
graph = [[] for _ in range(V + 1)]
indegree = [0 for _ in range(V + 1)]

for _ in range(E):
    a, b = map(int, input().split())
    graph[a].append(b)
    indegree[b] += 1

topology_sort()
```

위 코드에 다음과 같은 입력값을 넣어보자.

```
4 4
1 2
1 3
3 2
2 4
```

그러면 출력 결과는 다음과 같이 잘 나온다.

```
1 3 2 4
```

반면, 사이클이 있는 다음과 같은 코드를 넣어보겠다.

```
4 4
1 2
3 1
2 4
4 3
```

그러면 출력 결과는 다음과 같다.

```
cycle
```