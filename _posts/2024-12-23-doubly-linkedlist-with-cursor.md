---
layout: post
title: 양방향 연결 리스트(Doubly Linked List)와 커서(Cursor)
categories: Data-Structure
description: 백준 1406번 에디터
date: 2024-12-23 11:03:00 +0900
---
양방향 연결 리스트(Doubly Linked List)를 사용하여 수많은 삽입, 삭제 연산을 할 때에 이를 효율적으로 하려면 <b>커서(Cursor)</b> 개념을 도입해야 한다. 만약 커서가 없다면 연결 리스트에 들어가는 데이터의 개수가 많아졌을 때 매번 ```head``` 나 ```tail``` 에서부터 탐색을 해야 하므로 매우 비효율적이게 되는 것이다.

커서는 다양한 방식으로 구현할 수 있겠지만, 이번에는 두 노드 사이에 커서를 주는 방식으로 하여 이 커서를 ```(앞 노드, 뒤 노드)``` 로 표현할 것이다. 이러한 방식은 실제 컴퓨터 마우스 커서를 두는 방식이기도 하고, 그냥 어떤 노드를 가리키는 것보다 유연하게 사용할 수 있다는 장점이 있다. (물론 문제에서 다른 방식을 요구하면 그것에 맞추어야 할 것이다.)

이렇게 노드들의 맨 앞, 맨 뒤 혹은 두 노드 사이 어딘가가 되므로, 전체 노드의 개수를 V 개라 한다면, 커서(Cursor)가 위치할 수 있는 곳의 개수는 V + 1 이다.

커서 개념을 도입할 때에는, 커서 관련 메서드를 수행하는 게 아닌 이상 ```self.cursor```의 값을 업데이트하지 않아도 된다. 예를 들어 현재 연결 리스트에 값들이 들어있고, 맨 뒤에 값을 추가하는 ```insert()```와 같은 메서드를 추가적으로 수행할 때 굳이 커서의 값을 업데이트할 필요 없는 것이다. <b>즉 커서는 연결 리스트에 완전히 의존적이지는 않은, 약간의 독립성을 지닌 보조 도구라 할 수 있다.</b> 커서는 연결 리스트의 현재 위치를 추적하기 위한 도구일 뿐, 리스트의 전체 구조에 영향을 미치지는 않는다.

<br>

<a href="https://www.acmicpc.net/problem/1406" target="_blank">백준 1406번 에디터</a> 문제를 해결하려면 양방향 연결 리스트에 커서 개념을 도입해야 한다.

정답 코드는 다음과 같다.

```python
import sys
input = sys.stdin.readline


class Node:
    def __init__(self, data, prev=None, next=None):
        self.data = data
        self.prev = prev
        self.next = next


class NodeMgmt:
    def __init__(self, data):
        if data is not None:
            self.head = Node(data)
            self.tail = self.head
            self.cursor = (self.tail, None)
            self.size = 1
        else:
            self.head = None
            self.tail = None
            self.cursor = (None, None)
            self.size = 0

    def insert(self, data):
        if self.head is None:
            self.head = Node(data)
            self.tail = self.head
        else:
            node = self.tail
            new_node = Node(data)
            node.next = new_node
            new_node.prev = node
            self.tail = new_node
        self.size += 1

    def reset_cursor(self):     # 문제의 조건대로 커서를 tail 오른쪽에 둔다.
        self.cursor = (self.tail, None)

    def cursor_move_left(self):     # 커서 한 칸 왼쪽으로 이동
        prev_n, next_n = self.cursor
        if prev_n is not None:
            self.cursor = (prev_n.prev, prev_n)

    def cursor_move_right(self):    # 커서 한 칸 오른쪽으로 이동
        prev_n, next_n = self.cursor
        if next_n is not None:
            self.cursor = (next_n, next_n.next)

    def insert_at_cursor(self, data):       # '$라는 문자를 커서 왼쪽에 추가함'
        prev_n, next_n = self.cursor
        new_node = Node(data)

        if prev_n is None and next_n:       # head 왼쪽일 때
            new_node.next = next_n
            next_n.prev = new_node
            self.head = new_node
            self.cursor = (self.head, next_n)
        elif prev_n and next_n is None:     # tail 오른쪽일 때
            prev_n.next = new_node
            new_node.prev = prev_n
            self.tail = new_node
            self.cursor = (self.tail, None)
        elif prev_n and next_n:     # 중간에 삽입할 때
            prev_n.next = new_node
            new_node.prev = prev_n
            new_node.next = next_n
            next_n.prev = new_node
            self.cursor = (new_node, next_n)
        else:       # 빈 리스트에 추가
            self.head = new_node
            self.tail = self.head
            self.cursor = (self.tail, None)
        self.size += 1

    def delete_at_cursor(self):     # 커서 왼쪽에 있는 문자를 삭제함 (커서가 문장의 맨 앞이면 무시됨)
        prev_n, next_n = self.cursor
        if prev_n:      # 삭제할 노드가 있는 경우에만 진행
            if prev_n.prev:     # 중간 또는 tail 삭제
                prev_n.prev.next = next_n
            else:       # head 삭제
                self.head = next_n
            if next_n:      # 삭제 후 next_n 이 있으면 연결한다.
                next_n.prev = prev_n.prev
            else:
                self.tail = prev_n.prev
            self.cursor = (prev_n.prev, next_n)
            del prev_n
            self.size -= 1

    def desc(self):     # 마지막에 답안 출력하는 메서드
        result = []
        node = self.head
        while node:
            result.append(node.data)
            node = node.next
        print("".join(result))


input_string = list(input().strip())
N = input_string.__len__()
doubly = NodeMgmt(None)
for word in input_string:
    doubly.insert(word)
doubly.reset_cursor()

M = int(input())
for _ in range(M):
    cmd = list(input().strip().split())
    if cmd[0] == 'L':
        doubly.cursor_move_left()
    elif cmd[0] == 'D':
        doubly.cursor_move_right()
    elif cmd[0] == 'B':
        doubly.delete_at_cursor()
    elif cmd[0] == 'P':
        doubly.insert_at_cursor(cmd[1])

####################################################################
# 출력
# 첫째 줄에 모든 명령어를 수행하고 난 후 편집기에 입력되어 있는 문자열을 출력한다.

doubly.desc()
```

<br>

해당 문제에서는 '명령어가 수행되기 전에 커서는 문장의 맨 뒤에 위치하고 있다고 한다.' 라는 조건이 있기에 ```doubly```를 생성한 이후에 ```doubly.reset_cursor()``` 를 하여 커서를 ```tail``` 오른쪽에 두었다.

그리고 삭제 명령이나 삽입 명령의 경우 '커서의 왼쪽'에서 수행한다는 조건에 맞춰 구현하였다.