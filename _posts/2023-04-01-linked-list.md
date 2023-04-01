---
layout: post
title: 링크드 리스트(Linked List) 이해 및 구현
categories: Data-Structure
description: FastCampus 개발자 취업 합격 패스 Chapter 07. 링크드 리스트
date: 2023-04-01 22:07:00 +09:00
---
링크드 리스트(Linked List) 구조는 연결 리스트라고도 한다.

배열이 순차적으로 연결된 공간에 데이터를 나열하는 구조라면, 링크드 리스트는 떨어진 곳에 존재하는 데이터를 화살표로 연결해서 관리하는 구조이다.

본래 C언어에서는 주요한 데이터 구조이지만, 파이썬은 리스트 타입이 링크드 리스트의 기능을 모두 지원한다.


#### 링크드 리스트 기본 구조와 용어

* 노드(Node): 데이터 저장 단위 (데이터값, 포인터)로 구성
* 포인터(Pointer): 각 노드 안에서, 다음이나 이전의 노드와의 연결 정보를 가지고 있는 공간


#### 일반적인 링크드 리스트 형태

![wikipedia, https://en.wikipedia.org/wiki/Linked_list](https://user-images.githubusercontent.com/88493727/229291085-fb3e2536-90a3-4f43-90f3-fed132828666.png "wikipedia, https://en.wikipedia.org/wiki/Linked_list")


#### 링크드 리스트의 장단점 (전통적인 C 언어에서의 배열과 링크드 리스트)

* 장점
  * 데이터 공간을 미리 할당하지 않아도 됨. 배열은 미리 데이터 공간을 할당해야 한다.
* 단점
  * 연결을 위한 별도 데이터 공간이 필요하므로, 저장 공간 효율이 높지 않음
  * 연결 정보를 찾는 시간이 필요하므로 접근 속도가 느림
  * 중간 데이터 삭제 시, 앞뒤 데이터의 연결을 재구성해야 하는 부가적인 작업 필요


#### 파이썬 객체지향 프로그래밍으로 링크드 리스트 구현하기

```python
class Node:
    def __init__(self, data, next=None):
        self.data = data
        self.next = next
    
class NodeMgmt:
    def __init__(self, data):
        self.head = Node(data)
        
    def add(self, data):
        if self.head == '':
            self.head = Node(data)
        else:
            node = self.head
            while node.next:
                node = node.next
            node.next = Node(data)
            
    def middleAdd(self, prevData, addData):
        node = self.head
        while node:
            if node.data == prevData:
                break
            else:
                node = node.next
        if node is None:
            print("해당 연결 리스트에는 찾으시는 노드가 존재하지 않습니다.")
            return

        newNode = Node(addData)
        node_next = node.next
        node.next = newNode
        newNode.next = node_next
        
    def desc(self):
        node = self.head
        while node:
            print (node.data)
            node = node.next
    
    def delete(self, data):
        if self.head == '':
            print ("해당 값을 가진 노드가 없습니다.")
            return
        
        if self.head.data == data:
            temp = self.head
            self.head = self.head.next
            del temp
        else:
            node = self.head
            while node.next:
                if node.next.data == data:
                    temp = node.next
                    node.next = node.next.next
                    del temp
                    return
                else:
                    node = node.next
```

이렇게 된다. 이 코드를 테스트 해보면,

```python
# 테스트를 위해서 1개 노드를 만들어 봄
linkedList1 = NodeMgmt(0)
linkedList1.desc()	# 0

# head가 살아있음을 확인
print(linkedList1.head)	# <__main__.Node object at 0x10310dfa0> 살아있음

# head를 지워보고 출력
linkedList1.delete(0)
print(linkedList1.head)	# None

# 다시 하나의 노드 만들고, 여러 노드 추가한 다음, 노드 중에 한 개 삭제
linkedList1 = NodeMgmt(0)
for data in range(1, 10):
    linkedList1.add(data)
    
linkedList1.delete(4)
linkedList1.desc()	# 데이터 값이 4인 노드가 삭제된 것을 알 수 있다.
```

이렇게 4가 성공적으로 삭제된 채 출력되는 등 잘 작동하는 것을 알 수 있다.
