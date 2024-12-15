---
layout: post
title: 이중 연결 리스트 (Doubly Linked List) 완전 구현
categories: Data-Structure
date: 2024-12-15 13:37:00 +0900
---
이중 연결 리스트(Doubly Linked List)를 파이썬으로 구현하였다. 이전에 구현했던 이중 연결 리스트보다 많은 기능들을 넣어 완성도를 높였다.

구현 코드는 다음과 같다.

```python
class Node:
    def __init__(self, data, prev=None, next=None):
        self.data = data
        self.prev = prev
        self.next = next


class NodeMgmt:
    def __init__(self, data):
        if data is not None:    # 초기 데이터가 있을 경우, 해당 데이터를 포함하는 노드를 생성
            self.head = Node(data)
            self.tail = self.head
            self.size = 1
        else:       # 초기 데이터가 없을 경우, 빈 리스트로 설정
            self.head = None
            self.tail = None
            self.size = 0

    def insert(self, data):     # 리스트의 끝에 새로운 노드를 추가하는 메서드
        if self.head is None:   # 리스트가 비어 있으면 첫 노드를 생성한다.
            self.head = Node(data)
            self.tail = self.head
        else:       # 리스트가 비어있지 않으면 tail 에 새로운 노드를 추가한다.
            node = self.head
            while node.next:
                node = node.next
            newNode = Node(data)
            node.next = newNode
            newNode.prev = node
            self.tail = newNode
        self.size += 1

    def search_from_head(self, data):       # 리스트의 head 부터 데이터를 검색하는 메서드
        if self.head is None:
            print("The list is empty.")
            return False
        node = self.head
        while node:
            if node.data == data:
                return node
            else:
                node = node.next
        print(f'Data {data} not found in the list.')
        return False

    def search_from_tail(self, data):       # 리스트의 tail 부터 데이터를 검색하는 메서드
        if self.head is None:
            print("The list is empty.")
            return False
        node = self.tail
        while node:
            if node.data == data:
                return node
            else:
                node = node.prev
        return False

    def insert_before(self, data, before_data):     # 특정 데이터 앞에 새 노드를 삽입하는 메서드
        if self.head is None:
            self.head = Node(data)
            self.tail = self.head
            self.size += 1
            return True

        node = self.head
        while node and node.data != before_data:
            node = node.next        # before_data 가 나올 때까지 이동한다.

        if node is None:
            return False        # before_data 를 찾지 못한 경우 False 반환한다.

        newNode = Node(data)
        if node.prev is None:       # 삽입 위치가 head 인 경우
            self.head = newNode
            newNode.next = node
            node.prev = newNode
        else:       # 삽입 위치가 중간인 경우
            before_new = node.prev
            before_new.next = newNode
            newNode.prev = before_new
            newNode.next = node
            node.prev = newNode
        self.size += 1
        return True

    def insert_after(self, data, after_data):       # 특정 데이터 뒤에 새 노드를 삽입하는 메서드
        if self.head is None:
            self.head = Node(data)
            self.tail = self.head
            self.size += 1
            return True

        node = self.head
        while node and node.data != after_data:
            node = node.next        # after_data 가 나올 때까지 이동한다.
        if node is None:
            return False        # after_data 를 찾지 못한 경우 False 반환한다.

        newNode = Node(data)
        after_new = node.next
        newNode.next = after_new
        newNode.prev = node
        node.next = newNode
        if after_new is None:       # 삽입 위치가 tail 인 경우
            self.tail = newNode
        else:       # 삽입 위치가 중간일 경우
            after_new.prev = newNode

        self.size += 1
        return True

    def desc(self):         # 리스트의 데이터를 정방향으로 출력하는 메서드
        if self.head is None:
            print('The list is empty.')
            return
        node = self.head
        while node:
            print(node.data, end=' ')
            node = node.next
        print()

    def reverse_desc(self):     # 리스트의 데이터를 역방향으로 출력하는 메서드
        if self.tail is None:
            print('The list is empty.')
            return
        node = self.tail
        while node:
            print(node.data, end=' ')
            node = node.prev
        print()

    def delete(self, data):     # 리스트에서 특정 데이터를 가진 노드를 삭제하는 메서드
        if self.head is None:
            print("The list is empty.")
            return False

        node = self.head
        while node:
            if node.data == data:
                if node.prev is None:       # 삭제할 노드가 head 인 경우
                    self.head = node.next
                    if self.head:
                        self.head.prev = None
                elif node.prev and node.next:       # 삭제할 노드가 중간인 경우
                    node.prev.next = node.next
                    node.next.prev = node.prev
                elif node.next is None:     # 삭제할 노드가 tail 인 경우
                    self.tail = node.prev
                    if self.tail:
                        self.tail.next = None
                del node
                self.size -= 1
                return True
            node = node.next
        print(f"Data {data} not found in the list.")
        return False

    def delete_all(self, data):     # 리스트에서 특정 데이터를 가진 모든 노드를 삭제하는 메서드
        if self.head is None:
            print("The list is empty")
            return False

        deleted = False
        node = self.head
        while node:
            if node.data == data:
                if node.prev:
                    node.prev.next = node.next
                else:
                    self.head = node.next
                if node.next:
                    node.next.prev = node.prev
                else:
                    self.tail = node.prev
                temp = node
                node = node.next
                del temp
                self.size -= 1
                deleted = True
            else:
                node = node.next

        if not deleted:
            print(f"Data {data} not found in the list.")
        return deleted

    def clear(self):        # 리스트를 초기화하는 메서드
        self.head = None
        self.tail = None
        self.size = 0
        print("The list has been cleared")

    def get_size(self):     # 리스트의 크기를 반환하는 메서드
        return self.size
```

<br>

위 코드에서 구현하고 있는 이중 연결 리스트의 메서드들은 다음과 같다.

* ```__init__()``` 매직 메서드
* 리스트의 끝에 새로운 노드를 추가하는 ```insert()``` 메서드
* 리스트의 head 부터 데이터를 검색하는 ```search_from_head()``` 메서드
* 리스트의 tail 부터 데이터를 검색하는 ```search_from_tail()``` 메서드
* 특정 데이터 앞에 새 노드를 삽입하는 ```insert_before()``` 메서드
* 특정 데이터 뒤에 새 노드를 삽입하는 ```insert_after()``` 메서드
* 리스트의 데이터를 정방향으로 출력하는 ```desc()``` 메서드
* 리스트의 데이터를 역방향으로 출력하는 ```reverse_desc()``` 메서드
* 리스트에서 특정 데이터를 가진 노드를 삭제하는 ```delete()``` 메서드
* 리스트에서 특정 데이터를 가진 모든 노드를 삭제하는 ```delete_all()``` 메서드
* 리스트를 초기화 하는 ```clear()``` 메서드
* 리스트의 크기를 반환하는 ```get_size()``` 메서드

위 12개의 메서드를 ```NodeMgmt``` 클래스에서 다룸으로써, 이중 연결 리스트를 매우 완성도 높게 사용할 수 있다.

<br>

위 코드를 실제로 사용하는 예시를 보겠다.

