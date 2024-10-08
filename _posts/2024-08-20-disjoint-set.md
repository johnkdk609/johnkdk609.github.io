---
layout: post
title: 서로소 집합 알고리즘
categories: Algorithm
description: 나동빈, 2021, [이것이 취업을 위한 코딩 테스트다 with 파이썬], 한빛미디어
date: 2024-08-20 23:54:00 +0900
---
서로소 집합(Disjoint Set)이란 공통되는 원소가 없는 두 집합을 의미한다.

집합 {1, 2} 와 집합 {3, 4} 는 서로소 관계이다. 반면 집합 {1, 2} 와 집합 {2, 3} 은 2라는 원소가 공통적으로 포함돼 있으므로 서로소 관계가 아니다.

이러한 서로소 집합 자료구조는 <b>Union</b>, <b>Find</b> 의 두 가지 연산으로 조작할 수 있다.

<br>

## 유니온-파인드 (Union-Find)

유니온-파인드에서 유니온(Union)은 합집합 연산을 의미한다. 그리고 파인드(Find)는 루트 노드를 찾는 찾기 연산을 의미한다.

<br>

유니온-파인드의 기본 접근 방식은 다음과 같다.

```
1번. union (합집합) 연산을 확인하여 서로 연결된 두 노드 A, B를 확인한다.
    Ⅰ. A와 B의 루트 노드 A', B' 를 각각 찾는다. (find 연산)
    Ⅱ. A' 를 B' 의 부모 노드로 설정한다. (B' 가 A' 를 가리키도록 한다.)

2번. 모든 union (합집합) 연산을 처리할 때까지 위 1번 과정을 반복한다.
```

<br>

유니온-파인드를 코드로 구현하면 다음과 같다.

```python
# 특정 원소가 속한 집합을 찾기
def find_parent(parent, x):
    # 루트 노드가 아니라면, 루트 노드를 찾을 때까지 재귀적으로 호출
    if parent[x] != x:
        parent[x] = find_parent(parent, parent[x])
    return parent[x]

# 두 원소가 속한 집합을 합치기
def union_parent(parent, a, b):
    a = find_parent(parent, a)
    b = find_parent(parent, b)
    if a < b:
        parent[b] = a
    else:
        parent[a] = b

# 노드의 개수와 간선(union 연산)의 개수 입력 받기
v, e = map(int, input().split())
parent = [0] * (v + 1)  # 부모 테이블 초기화

# 부모 테이블상에서, 부모를 자기 자신으로 초기화
for i in range(1, v + 1):
    parent[i] = i

# union 연산을 각각 수행
for i in range(e):
    a, b = map(int, input().split())
    union_parent(parent, a, b)

# 각 원소가 속한 집합 출력
print('각 원소가 속한 집합: ', end='')
for i in range(1, v + 1):
    print(find_parent(parent, i), end=' ')

print()

# 부모 테이블 내용 출력
print('부모 테이블: ', end='')
for i in range(1, v + 1):
    print(parent[i], end=' ')
```

위 코드는 경로 압축 기법이 적용되어 있다.

<br>

## 경로 압축 기법

유니온-파인드에서는 '<b>경로 압축(Path Compression)</b>' 이라는 것이 이뤄진다. 이러한 경로 압축은 'skewed tree' 를 방지하기 위해 진행된다.

다음 그림을 보자.

<img width="420" alt="skewed tree" src="https://github.com/user-attachments/assets/81bbadf2-8b8a-4206-9e8f-70c67091e70b">

이렇게 마치 꼬챙이에 끼워진 듯한 느낌으로, 일렬로 되어 있는 트리를 'skewed tree'라고 한다.

이런 skewed tree 에서는 1, 2, 3, 4, 5 노드의 루트 노드가 1 노드이다. 끝에 있는 5의 경우 루트 노드를 찾기 위해 4-3-2-1 의 순으로 탐색을 해야 한다.

이렇게 되면 루트 노드를 찾는 데에 시간이 많이 걸리게 된다. 이때 경로 압축을 진행하면 시간을 크게 단축시킬 수 있다.

<br>

<img width="350" alt="compressed tree" src="https://github.com/user-attachments/assets/bb872adc-63b5-4c45-ab05-8013fa0e6f23">

위 그림은 경로 압축(Path Compression)을 수행한 트리이다. 2, 3, 4, 5 노드가 모두 바로 부모 노드로 루트 노드를 가리키고 있다.

이렇게 되면 2, 3, 4, 5 노드에서 한 깊이(depth)만 가면 루트 노드를 찾을 수 있으므로, 시간을 크게 개선할 수 있다.

<br>

위 코드에서 경로 압축을 진행하는 부분은 다음과 같다.

```python
def find_parent(parent, x):
    if parent[x] != x:
        parent[x] = find_parent(parent, parent[x])
    return parent[x]
```

경로 압축은 find 함수를 재귀적으로 호출한 뒤에 부모 테이블 값을 갱신하는 기법이다.

각 노드에 대하여 find 함수를 호출한 이후에, 해당 노드의 루트 노드가 바로 부모 노드가 된다.

<br>

## 서로소 집합을 활용한 사이클 판별

서로소 집합을 사용하면 사이클 판별이 가능하다.

사이클 판별의 로직은 다음과 같다.

```
1번. 각 간선을 확인하며 두 노드의 루트 노드를 확인한다.
    Ⅰ. 루트 노드가 서로 다르다면 두 노드에 대하여 union 연산을 수행한다.
    Ⅱ. 루트 노드가 서로 같다면 사이클(Cycle)이 발생한 것이다.

2번. 그래프에 포함되어 있는 모든 간선에 대하여 1번 과정을 반복한다.
```

<br>

사이클 판별을 수행하는 코드는 다음과 같다.

```python
# 특정 원소가 속한 집합을 찾기
def find_parent(parent, x):
    # 루트 노드가 아니라면, 루트 노드를 찾을 때까지 재귀적으로 호출
    if parent[x] != x:
        parent[x] = find_parent(parent, parent[x])
    return parent[x]

# 두 원소가 속한 집합을 합치기
def union_parent(parent, a, b):
    a = find_parent(parent, a)
    b = find_parent(parent, b)
    if a < b:
        parent[b] = a
    else:
        parent[a] = b

# 노드의 개수와 간선(union 연산)의 개수 입력 받기
v, e = map(int, input().split())
parent = [0] * (v + 1)  # 부모 테이블 초기화

# 부모 테이블상에서, 부모를 자기 자신으로 초기화
for i in range(1, v + 1):
    parent[i] = i

cycle = False   # 사이클 발생 여부

for i in range(e):
    a, b = map(int, input().split())
    # 사이클이 발생한 경우 종료
    if find_parent(parent, a) == find_parent(parent, b):
        cycle = True
        break
    # 사이클이 발생하지 않았다면 합집합(union) 수행
    else:
        union_parent(parent, a, b)

if cycle:
    print("사이클이 발생했습니다.")
else:
    print("사이클이 발생하지 않았습니다.")
```