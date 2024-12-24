---
layout: post
title: 원형 연결 리스트(Circular Linked List)와 커서(Cursor)
categories: Data-Structure
description: 백준 1021번 회전하는 큐
date: 2024-12-24 17:06:00 +0900
---
<b>원형 연결 리스트(Circular Linked List)</b>란 맨 앞 노드와 맨 뒤 노드가 연결돼 있는 형태의 연결 리스트이다.

이중 연결 리스트(Doubly Linked List)에서 맨 앞쪽 시작점인 ```head```와 맨 뒤쪽 시작점인 ```tail```을 관리하는 것과는 달리, 원형 링크드 리스트에서는 ```tail``` 을 관리할 필요가 없다. 시작점으로 상정한 ```head``` 만 있으면 되는 것이다.

또, 리스트에 노드가 하나라도 들어가 있다면 해당 노드의 이전, 이후인 ```prev```, ```next``` 가 ```None``` 이 될 수 없다. 왜냐하면 연결 리스트에 노드가 하나 있다면 그 노드의 이전 노드는 자기 자신이고, 다음 노드도 자기 자신이기 때문이다.

<br>

앞서 양방향 연결 리스트에서 커서(Cursor)를 사용해 효율적으로 삽입, 삭제 연산을 수행했듯이 원형 연결 리스트에서도 커서 개념을 도입할 수 있다. 커서의 경우 (앞 노드, 뒤 노드) 의 방식으로 하여 그 사이를 가리키게 할 수 있지만, 이번에는 그냥 특정 노드를 가리키는 방식으로 구현하였다. 이는 문제에서 요구하는 방식에 맞춰서 선택하면 되는 것이다.

특정 노드를 가리키는 방식으로 구현했기 때문에, 몇 가지 규칙을 임의로 정해야 한다. 일단 맨 앞 노드인 ```self.head``` 를 가리키는 것으로 시작해야 한다는 것이고, 삽입하면 현재 노드 기준 왼쪽에 삽입하고 커서를 새로 삽입한 노드를 가리키게 해야 한다는 것이며, 마지막으로 삭제할 때에는 현재 가리키고 있는 노드를 삭제하고 그 다음(오른쪽) 노드로 커서를 옮겨야 한다는 것이다.

<br>

이러한 특징 및 규칙들을 고려하여 원형 연결 리스트를 구현하면 다음과 같다.

```python
class Node:
    def __init__(self, data, prev=None, next=None):
        self.data = data
        self.prev = prev
        self.next = next


class NodeMgmt:
    def __init__(self, data):
        if data is not None:
            self.head = Node(data)
            self.head.next = self.head
            self.head.prev = self.head
            self.cursor = self.head
            self.size = 1
        else:
            self.head = None
            self.cursor = None
            self.size = 0

    def insert(self, data):     # head의 왼쪽 즉 맨 마지막에 새 노드를 추가하는 메서드
        if self.head is None:
            self.head = Node(data)
            self.head.next = self.head
            self.head.prev = self.head
        else:
            new_node = Node(data)
            self.head.prev.next = new_node
            new_node.prev = self.head.prev
            new_node.next = self.head
            self.head.prev = new_node
        self.size += 1

    def reset_cursor(self):     # 커서를 head 로 초기화하는 메서드
        self.cursor = self.head

    def cursor_move_left(self):     # 커서를 왼쪽으로 한 칸 움직이는 메서드
        if self.cursor:
            self.cursor = self.cursor.prev

    def cursor_move_right(self):    # 커서를 오른쪽으로 한 칸 움직이는 메서드
        if self.cursor:
            self.cursor = self.cursor.next

    def insert_at_cursor(self, data):       # 현재 커서가 가리키고 있는 노드 왼쪽에 새 노드를 추가하는 메서드
        current = self.cursor
        new_node = Node(data)
        if current is None:
            self.head = new_node
            new_node.prev = new_node
            new_node.next = new_node
            self.cursor = new_node
        else:       # 왼쪽에 추가한다고 가정. 추가하고 커서는 추가한 곳 가리킨다.
            current.prev.next = new_node
            new_node.prev = current.prev
            new_node.next = current
            current.prev = new_node
            self.cursor = new_node
        self.size += 1

    def delete_at_cursor(self):     # 커서가 가리키고 있는 것 삭제하고 오른쪽(다음) 노드로 커서 업데이트 하는 메서드
        current = self.cursor
        if current:
            if current != current.prev:     # 노드가 2개 이상인 경우
                if current == self.head:
                    current.prev.next = current.next
                    current.next.prev = current.prev
                    self.head = current.next
                else:
                    current.prev.next = current.next
                    current.next.prev = current.prev
                self.cursor = current.next
            else:       # 노드가 1개인 경우
                self.head = None
                self.cursor = None
            self.size -= 1
            del current

    def desc(self):     # head 부터 전체 노드 출력하는 메서드
        if self.head is None:
            print("The list is empty.")
        else:
            node = self.head
            result = []
            while True:
                result.append(str(node.data))
                if node.next == self.head:
                    break
                node = node.next
            print(" <-> ".join(result))

    def get_size(self):     # 현재 원형 연결 리스트에 들어있는 노드의 개수 리턴하는 메서드
        return self.size

    def get_cursor_data(self):      # 현재 커서가 가리키고 있는 노드의 데이터 리턴하는 메서드
        return self.cursor.data
```

<br>

이제 위 코드에 대해 다음과 같이 테스트를 해봤다.

```python
CLL = NodeMgmt(None)        # # CLL 생성 및 1 ~ 5 의 자연수 insert
for i in range(1, 6):
    CLL.insert(i)
CLL.desc()
# 출력 결과 : 1 <-> 2 <-> 3 <-> 4 <-> 5

CLL.reset_cursor()      # 커서 리셋하고, 현재 커서가 가리키고 있는 노드 데이터 출력
print(CLL.get_cursor_data())
# 출력 결과 : 1

for _ in range(2):      # 커서 왼쪽으로 두 칸 이동하고, 현재 커서가 가리키고 있는 노드 데이터 출력
    CLL.cursor_move_left()
print(CLL.get_cursor_data())
# 출력 결과 : 4

CLL.insert_at_cursor(10)    # 현재 커서 가리키고 있는 곳 왼쪽에 10을 값으로 하는 노드 추가하고 출력, 현재 커서 가리키고 있는 노드 데이터 출력
CLL.desc()
# 출력 결과 : 1 <-> 2 <-> 3 <-> 10 <-> 4 <-> 5
print(CLL.get_cursor_data())
# 출력 결과 : 10

for _ in range(3):      # 커서 오른쪽으로 세 칸 이동
    CLL.cursor_move_right()

CLL.delete_at_cursor()      # 현재 커서가 가리키는 노드 삭제하고, 연결 리스트 출력 및 현재 커서가 가리키는 노드 데이터 출력
CLL.desc()
# 출력 결과 : 2 <-> 3 <-> 10 <-> 4 <-> 5
print(CLL.get_cursor_data())
# 출력 결과 : 2
```

<br>

<hr>

<br>

<a href="https://www.acmicpc.net/problem/1021" target="_blank">백준 1021번 회전하는 큐</a> 문제는 원형 연결 리스트를 사용하여 해결해야 하는 문제이다.

문제에서 요구하는 '최솟값'을 구하기 위해서는 왼쪽으로 순회하는 경우, 그리고 오른쪽으로 순회하는 경우 두 가지를 다 체크해야 한다. 이것을 find_shortest_steps_and_delete() 메서드로 구현하였다.

답안 코드는 다음과 같다.

```python
class Node:
    def __init__(self, data, prev=None, next=None):
        self.data = data
        self.prev = prev
        self.next = next


class NodeMgmt:
    def __init__(self, data):
        if data is not None:
            self.head = Node(data)
            self.head.next = self.head
            self.head.prev = self.head
            self.cursor = self.head
            self.size = 1
        else:
            self.head = None
            self.cursor = None
            self.size = 0

    def insert(self, data):
        if self.head is None:
            self.head = Node(data)
            self.head.next = self.head
            self.head.prev = self.head
        else:
            new_node = Node(data)
            self.head.prev.next = new_node
            new_node.prev = self.head.prev
            new_node.next = self.head
            self.head.prev = new_node
        self.size += 1

    def reset_cursor(self):
        self.cursor = self.head

    def find_shortest_steps_and_delete(self, data):
        current_start = self.cursor
        if current_start:
            left = right = 0    # 초기화
            # 왼쪽 순회
            node = current_start
            while True:
                if node.data == data:
                    break
                node = node.prev
                left += 1
            # 오른쪽 순회
            node = current_start
            while True:
                if node.data == data:
                    break
                node = node.next
                right += 1
            # 이제 지울 것 지우고, 앞뒤 연결해주고, 최솟값 리턴한다.
            if node != node.next:   # 2개 이상 연결된 상황
                node.prev.next = node.next
                node.next.prev = node.prev
                if node == self.head:
                    self.head = node.next
                self.cursor = node.next
            else:       # 1개만 있는 상황
                self.head = None
                self.cursor = None
            self.size -= 1
            del node
            return min(left, right)
        return False


N, M = map(int, input().split())

CLL = NodeMgmt(None)
for i in range(1, N + 1):
    CLL.insert(i)
CLL.reset_cursor()
numbers = list(map(int, input().split()))

total = 0
for i in range(M):
    total += CLL.find_shortest_steps_and_delete(numbers[i])

print(total)
```