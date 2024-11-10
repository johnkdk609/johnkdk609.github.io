---
layout: post
title: 위상 정렬 알고리즘 (Topological Sort Algorithm)
categories: Algorithm
date: 2024-11-08 21:42:00 +0900
---
위상 정렬(Topological Sort)이란 유향 그래프의 정점들을 간선의 방향을 거스르지 않도록 나열하는 정렬 방법이다.

순서가 정해져 있는 작업들을 차례대로 수행해야 할 때, 그 순서를 결정해준다.

위상 정렬을 가장 잘 설명하는 문제는 선수과목(prerequisite) 구조가 있다. 어떤 과목을 수강하기 위해서 선수과목을 수강해야 한다면, 이러한 과목들의 수강 순서를 결정할 때 위상 정렬 알고리즘을 사용하면 되는 것이다.

<b>위상 정렬이 성립하기 위해서는 그래프에 <u>사이클(cycle, 순환)이 존재하면 안 된다.</u></b>

즉, 그래프가 <b>비순환 유향 그래프(directed acyclic graph)</b>여야 하는 것이다.

이러한 위상 정렬은 BFS 를 이용해서 구현할 수 있다. 위상 정렬 알고리즘을 구현한 코드는 다음과 같다.

```python
from collections import deque


def topological_sort():
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

topological_sort()
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

<br>

위 코드를 순서대로 보자. 우선 메인에서 정점(Vertex), 간선(Edge)의 개수를 입력 받는다. 그리고 노드(정점)은 1부터 시작하므로, 인접 리스트인 graph 를 생성할 때 1번부터 시작할 수 있도록 인덱스 처리를 했다. indegree 리스트의 경우, '진입차수'를 관리하는 리스트이다. 마찬가지로 정점이 1부터 시작하므로 인덱스 처리를 했다.

그리고 이제 간선의 개수만큼 순회를 돌면서, 인접 리스트인 graph 에 값을 넣고 그에 따라 진입차수 리스트(indegree)도 업데이트 한다. ```a, b``` 순서로 입력을 받는데, 이는 a 노드에서 b 노드로 간선이 향한다는 것이다. 즉, a 노드의 진출 차수이며 b 노드의 진입 차수이다. 그렇기에 ```indegree[b] += 1``` 를 하여 진입 차수 리스트를 업데이트 한다.

BFS 방식을 사용할 것이기 때문에, 코드 제일 상단에 deque 라이브러리를 임포트 했다.

이제 topological_sort() 메서드 안을 보자. 우선 queue 라는 deque() 자료구조를 생성한다. 그리고 정렬된 값들을 순서대로 담기 위해 result 리스트를 생성했다.

위상 정렬을 하기 위해서는 queue에 진입 차수가 0 인 정점들을 넣는다. 그래서 우선 BFS를 돌리기 전에, 현재 진입 차수가 0인 정점들 즉, 자기 자신으로 들어오는 간선이 없는 정점들을 queue에 ```append``` 한다.

이제 queue에 값이 남아있을 때까지 while 문으로 반복문을 돌린다. current 라는 변수에 queue 에서 막 꺼낸 정점을 넣고, 그 current 를 result 리스트에 담는다.

