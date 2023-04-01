---
layout: post
title: 큐와 스택의 특징 및 활용
categories: Data-Structure
date: 2023-04-01 17:55:00 +09:00
description: FastCampus 개발자 취업 합격 패스 Chapter 05, 06
---
### 큐 (Queue)

큐와 관련한 용어
* enqueue: 큐에 데이터를 넣는 기능
* dequeue: 큐에서 데이터를 꺼내는 기능


#### 파이썬 queue 라이브러리를 활용해서 큐 자료 구조 사용하기

* Queue(): 가장 일반적인 큐 자료 구조
* LifoQueue(): 나중에 입력된 데이터가 먼저 출력되는 구조 (스택 구조라고 보면 됨)
* PriorityQueue(): 데이터마다 우선순위를 넣어서, 우선순위가 높은 순으로 데이터 출력


예시로 PriorityQueue()를 사용해보겠다.

```python
import queue

data_queue = queue.PriorityQueue()

data_queue.put((10, "korea")) # 튜플 방식으로 해서, 왼쪽에는 우선순위를, 오른쪽에는 내용을 적는다.
data_queue.put((5, 1))
data_queue.put((15, "USA"))

data_queue.qsize()  # 3

print(data_queue.get()) # (5, 1)  # 셋 중 우선순위가 제일 높은 (5, 1)이 출력되는 것이다.

print(data_queue.get()) # (10, "korea")
```

이처럼 put, get으로 큐에 데이터를 추가하거나, 데이터를 빼낸다. get을 하고 나면, 큐에서 해당 데이터는 사라진다.


#### enqueue, dequeue 기능 구현해보기

```python
queue_list = list()

def enqueue(data):
  queue_list.append(data)
  
def dequeue():
  data = queue_list[0]
  del queue_list[0] # 큐에서 dequeue 하고 나서 해당 데이터 삭제
  return data
  
for i in range(10):
  enqueue(i)
  
print(len(queue_list)) # 10
print(dequeue())  # 0
print(dequeue())  # 1
print(dequeue())  # 2
print(dequeue())  # 3
```


#### 큐는 어디에 많이 쓰일까?

큐는 멀티 태스킹을 위한 프로세스 스케줄링 방식을 구현하기 위해 많이 사용된다. (운영체제 참조)

> 큐의 경우 장단점 보다는 (특별이 언급되는 장단점이 없음), 큐의 활용 예로 프로세스 스케줄링 방식을 함께 이해해두는 것이 좋다.



### 스택 (Stack)

스택은 데이터를 제한적으로 접근할 수 있는 구조이다. 한쪽 끝에서만 자료를 넣거나 뺄 수 있는 것.

스택의 주요 기능으로는 이런 것들이 있다.
* push(): 데이터를 스택에 넣기
* pop(): 데이터를 스택에서 꺼내기

이 기능들을 코드로 구현해보면 다음과 같다.

```python
stack_list = list()

def push(data):
  stack_list.append(data)
  
def pop():
  data = stack_list[-1]
  del stack_list[-1]  # 큐에서와 마찬가지로, pop()을 하고 나면 해당 데이터는 삭제해야 한다.
  return data
```


#### 자료 구조 스택의 장단점

* 장점
  * 구조가 단순해서 구현이 쉽다.
  * 데이터 저장/읽기 속도가 빠르다.
* 단점
  * 데이터 최대 갯수를 미리 정해야 한다. (파이썬의 경우 재귀 함수는 1,000번 까지만 호출이 가능하다.)
  * 저장 공간의 낭비가 발생할 수 있다. (미리 최대 갯수만큼 저장 공간을 확보해야 하기 때문)

> 스택은 단순하고 빠른 성능을 위해 사용되므로, 보통 배열 구조를 활용해서 구현하는 것이 일반적이다. 이 경우에, 위에서 열거한 단점이 있을 수 있다.

#### 스택은 어디에 많이 쓰일까?

대표적으로 스택이 활용되는 곳은 컴퓨터 내부의 프로세스 구조의 함수 동작 방식이다. 스택 구조는 프로세스 실행 구조의 가장 기본이며, 함수 호추 시 프로세스 실행 구조를 스택과 비교해서 이해할 필요가 있다.
