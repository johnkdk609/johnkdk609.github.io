---
layout: post
title: Stack과 Queue
description: Java의 정석 기초편 ch. 11 컬렉션 프레임웍
categories: Java
date: 2023-05-18 17:52:00 +09:00
---
스택은 마지막에 저장한 데이터를 가장 먼저 꺼내게 되는 LIFO(Last In First Out)구조로 되어 있고, 큐는 처음에 저장한 데이터를 가장 먼저 꺼내게 되는 FIFO(First In First Out)구조로 되어 있다.

순차적으로 데이터를 추가하고 삭제하는 **스택에는 ArrayList와 같은 배열 기반의 컬렉션 클래스가 적합**하지만, 큐는 데이터를 꺼낼 때 항상 첫 번째 저장된 데이터를 삭제하므로, ArrayList와 같은 배열 기반의 컬렉션 클래스를 사용한다면 데이터를 꺼낼 때마다 빈 공간을 채우기 위해 데이터의 복사가 발생하므로 비효율적이다. 그래서 **큐는 ArrayList보다 데이터의 추가/삭제가 쉬운 LinkedList로 구현하는 것이 더 적합**하다.

> Stack - ArrayList와 같은 배열 기반의 컬렉션 클래스가 적합\
> Queue - LinkedList와 같이 데이터의 추가/삭제가 용이한 것으로  적합

## Stack과 Queue의 메서드

<table>
  <tr>
    <th>메서드</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>boolean empty()</td>
    <td>Stack이 비어있는지 알려준다.</td>
  </tr>
  <tr>
    <td>Object peek()</td>
    <td>Stack의 맨 위에 저장된 객체를 반환. pop()과 달리 Stack에서 객체를 꺼내지는 않음. <br>(비었을 때는 EmptyStackException 발생)</td>
  </tr>
  <tr>
    <td>Object pop()</td>
    <td>Stack의 맨 위에 저장된 객체를 꺼낸다. (비었을 때는 EmptyStackException 발생)</td>
  </tr>
  <tr>
    <td>Object push(Object item)</td>
    <td>Stack에 객체(item)를 저장한다.</td>
  </tr>
  <tr>
    <td>int search(Object o)</td>
    <td>Stack에 주어진 객체(o)를 찾아서 그 위치를 반환. 못 찾으면 -1을 반환. <br>(배열과 달리 위치는 0이 아닌 1부터 시작. 맨 위의 요소가 1)</td>
  </tr>
</table>

위 표는 Stack의 메서드이다.

<table>
  <tr>
    <th>메서드</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>boolean add(Object o)</td>
    <td>지정된 객체를 Queue에 추가한다. 성공하면 true를 반환. 저장 공간이 부족하면 IllegalStateException 발생</td>
  </tr>
  <tr>
    <td>Object remove()</td>
    <td>Queue에서 객체를 꺼내 반환. 비어있으면 NoSuchElementException 발생</td>
  </tr>
  <tr>
    <td>Object element()</td>
    <td>삭제 없이 요소를 읽어온다. peek와 달리 Queue가 비었을 때 NoSuchElementException 발생</td>
  </tr>
  <tr>
    <td>boolean offer(Object o)</td>
    <td>Queue에 객체를 저장. 성공하면 true, 실패하면 false를 반환</td>
  </tr>
  <tr>
    <td>Object poll()</td>
    <td>Queue에서 객체를 꺼내서 반환. 비어있으면 null을 반환</td>
  </tr>
  <tr>
    <td>Object peek()</td>
    <td>삭제 없이 요소를 읽어 온다. Queue가 비어있으며 null을 반환</td>
  </tr>
</table>

위 표는 Queue의 메서드이다.


## Stack과 Queue 예제

```java
import java.util.*;

class Ex11_2 {
	public static void main(String[] args) {
		Stack st = new Stack();
		Queue q = new LinkedList();	// Queue인터페이스의 구현체인 LinkedList를 사용 
		
		st.push("0");
		st.push("1");
		st.push("2");

		q.offer("0");
		q.offer("1");
		q.offer("2");

		System.out.println("= Stack =");
		while(!st.empty()) {
			System.out.println(st.pop()); // 스택에서 요소 하나를 꺼내서 출력 
		}

		System.out.println("= Queue =");
		while(!q.isEmpty()) {
			System.out.println(q.poll()); // 큐에서 요소 하나를 꺼내서 출력 
		}
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
= Stack =
2
1
0
= Queue =
0
1
2
```

스택과 큐에 각각 "0", "1", "2"를 같은 순서로 넣고 꺼내었을 때의 결과가 다른 것을 알 수 있다. 큐는 먼저 넣은 것이 먼저 꺼내지는 구조(FIFO)이기 때문에 넣을 때와 같은 순서이고, 스택은 먼저 넣은 것이 나중에 꺼내지는 구조(LIFO)이기 때문에 넣을 때의 순서와 반대로 꺼내진 것을 알 수 있다. 

자바에서는 스택을 Stack클래스로 구현하여 제공하고 있지만 큐는 Queue인터페이스로만 정의해 놓았을 뿐 별도의 클래스를 제공하고 있지 않다. 대신 Queue인터페이스를 구현한 클래스들이 있어서 이들 중의 하나를 선택해서 사용하면 된다.
