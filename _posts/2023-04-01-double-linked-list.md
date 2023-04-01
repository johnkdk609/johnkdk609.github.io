---
layout: post
title: 더블 링크드 리스트(Double Linked List) 이해 및 구현
categories: Data-Structure
date: 2023-04-01 23:01:00 +09:00
description: FastCampus 개발자 취업 합격 패스 Chapter 07. 링크드 리스트
---
더블 링크드 리스트(Double Linked List)는 이중 연결 리스트라고도 한다.

* 장점: 양방향으로 연결되어 있어서 노드 탐색이 양쪽으로 모두 가능

![https://en.wikipedia.org/wiki/Linked_list](https://user-images.githubusercontent.com/88493727/229293853-af1c991e-459e-48ee-a01e-8faae0d39042.png "https://en.wikipedia.org/wiki/Linked_list")

링크드 리스트의 경우, 만약 노드가 10,000개인데 내가 찾는 데이터가 9,000번째에 있으면 0번부터 9,000번까지 찾아야 해서 시간이 오래 걸린다. 그런데 맨 뒤에서부터 검색하면 상대적으로 9,999번에서 9,000번까지 찾아가는 것이기에 검색량이 훨씬 적이지고 효율적이다. 더 빠르게 원하는 데이터를 찾을 수 있는 것이다.


#### 더블 링크드 리스트의 특징

* 노드마다 포인터를 두 개 가지고 있어 해당 노드의 앞 주소를 가리키는 포인터, 뒤 노드를 가리키는 포인터가 있다.


#### 더블 링크드 리스트 코드 구현

```python
class Node:
    def __init__(self, data, prev=None, next=None):
        self.prev = prev
        self.data = data
        self.next = next

class NodeMgmt:
    def __init__(self, data):
        self.head = Node(data)
        self.tail = self.head

    def insert(self, data):
        if self.head == None:
            self.head = Node(data)
            self.tail = self.head
        else:
            node = self.head
            while node.next:
                node = node.next
            new = Node(data)
            node.next = new
            new.prev = node
            self.tail = new

    def search_from_head(self, data):
        if self.head == None:
            return False

        node = self.head
        while node:
            if node.data == data:
                return node
            else:
                node = node.next
        return False

    def search_from_tail(self, data):
        if self.head == None:
            return False

        node = self.tail
        while node:
            if node.data == data:
                return node
            else:
                node = node.prev
        return False

    def insert_before(self, data, before_data):
        if self.head == None:
            self.head = Node(data)
            return True
        else:
            node = self.tail
            while node.data != before_data:
                node = node.prev
                if node == None:
                    return False

            new = Node(data)
            before_new = node.prev
            before_new.next = new
            new.prev = before_new
            new.next = node
            node.prev = new
            return True

    def insert_after(self, data, after_data):
        if self.head == None:
            self.head = Node(data)
            return True
        else:
            node = self.head
            while node.data != after_data:
                node = node.next
                if node == None:
                    return False
            
            new = Node(data)
            after_new = node.next
            new.next = after_new
            new.prev = node
            node.next = new
            if new.next == None:
                self.tail = new
            return True

    def desc(self):
        node = self.head
        while node:
            print(node.data)
            node = node.next
```

해당 코드를 테스트 해보겠다. 가령, insert_before 함수를 한 번 실행해보면,

```python
doubleLinkedList1 = NodeMgmt(0)
for i in range(1, 10):
    doubleLinkedList1.insert(i)

node3 = doubleLinkedList1.insert_before(1.5, 2)
doubleLinkedList1.desc()
```
1과 2 사이에 1.5가 삽입된 채 잘 출력되는 것을 알 수 있다.
