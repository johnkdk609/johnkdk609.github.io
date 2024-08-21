---
layout: post
title: 서로소 집합 알고리즘
categories: Algorithm
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

<b>1번. union (합집합) 연산을 확인하여 서로 연결된 두 노드 A, B를 확인한다.
    Ⅰ. A와 B의 루트 노드 A', B' 를 각각 찾는다. (find 연산)
    Ⅱ. A' 를 B' 의 부모 노드로 설정한다. (B' 가 A' 를 가리키도록 한다.)

2번. 모든 union (합집합) 연산을 처리할 때까지 위 1번 과정을 반복한다.</b>

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

