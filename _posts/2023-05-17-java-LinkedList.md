---
layout: post
title: LinkedList
description: Java의 정석 기초편 ch. 11 컬렉션 프레임웍
categories: Java
date: 2023-05-17 12:50:00 +09:00
---
배열은 가장 기본적인 형태의 자료구조로 구조가 간단하며 사용하기 쉽고 데이터를 읽어 오는 데 걸리는 시간(접근시간, access time)이 가장 빠르다는 장점을 가지고 있다. 하지만 다음과 같은 단점도 가지고 있다.

> 1. 크기를 변경할 수 없다.\
> \- 크기를 변경할 수 없으므로 새로운 배열을 생성해서 데이터를 복사해야 한다.\
> \- 실행 속도를 향상시키기 위해서는 충분히 큰 크기의 배열을 생성해야 하므로 메모리가 낭비된다.
>
> 2. 비순차적인 데이터의 추가 또는 삭제에 시간이 많이 걸린다.\
> \- 차례대로 데이터를 추가하고 마지막에서부터 데이터를 삭제하는 것은 빠르지만,\
> \- 배열의 중간에 데이터를 추가하려면, 빈자리를 만들기 위해 다른 데이터들을 복사해서 이동해야 한다.

이러한 **배열의 단점을 보완하기 위해서 링크드 리스트(linked list)라는 자료구조가 고안되었다.** 배열은 모든 데이터가 연속적으로 존재하지만 링크드 리스트는 불연속적으로 존재하는 데이터를 서로 연결(link)한 형태로 구성되어 있다.

<img width="750" alt="[자바의 정석 - 기초편] ch11-12~14 LinkedList" title="[자바의 정석 - 기초편] ch11-12~14 LinkedList" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/a26f0ee5-3736-4f3a-b158-0ce7e0568f53">

<img width="750" alt="[자바의 정석 - 기초편] ch11-12~14 LinkedList" title="[자바의 정석 - 기초편] ch11-12~14 LinkedList" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/6457a1b7-16e5-4d08-942b-011c7cfd4a0a">

위의 그림에서 알 수 있듯이 링크드 리스트의 각 요소(node)들은 자신과 연결된 다음 요소에 대한 참조(주솟값)와 데이터로 구성되어 있다.

```java
class Node {
  Node next;    // 다음 요소의 주소를 저장
  Object obj;   // 데이터를 저장
}
```


## LinkedList의 추가와 삭제

링크드 리스트에서의 데이터 삭제는 간단하다. 삭제하고자 하는 요소의 이전 요소가 삭제하고자 하는 요소의 다음 요소를 참조하도록 변경하기만 하면 된다. 단, 하나의 참조만 변경하면 삭제가 이루어지는 것이다. 배열처럼 데이터를 이동하기 위해 복사하는 과정이 없기 때문에 처리 속도가 매우 빠르다. 

<img width="750" alt="[자바의 정석 - 기초편] ch11-12~14 LinkedList" title="링크드 리스트에서의 데이터 삭제" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/72c72b28-e876-47c7-b689-5990fac312d2">

새로운 데이터를 추가할 때는 새로운 요소를 생성한 다음 추가하고자 하는 위치의 이전 요소의 참조를 새로운 요소에 대한 참조로 변경해주고, 새로운 요소가 그 다음 요소를 참조하도록 변경하기만 하면 되므로 처리 속도가 매우 빠르다.

<img width="750" alt="[자바의 정석 - 기초편] ch11-12~14 LinkedList" title="링크드 리스트에서의 데이터 추가" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/75319ac6-0b93-4e36-b25b-558766d61a8b">


## ArrayList와 LinkedList의 비교

배열의 경우 만일 인덱스가 n인 원소의 값을 얻어 오고자 한다면 단순히 아래와 같은 수식을 계산함으로써 해결된다.

> 인덱스가 n인 데이터의 주소 = 배열의 주소 + n * 데이터 타입의 크기

배열은 각 요소들이 연속적으로 메모리상에 존재하기 때문에 이처럼 간단한 계산만으로 원하는 요소의 주소를 얻어서 저장된 데이터를 곧바로 읽어올 수 있지만, LinkedList는 불연속적으로 위치한 각 요소들이 서로 연결된 것이라 처음부터 n번째 데이터까지 차례대로 따라가야만 원하는 값을 얻을 수 있다. 

그래서 **LinkedList는 저장해야 하는 데이터의 개수가 많아질수록 데이터를 읽어오는 시간, 즉 접근시간(access time)이 길어진다는 단점이 있다.**

<table>
  <tr>
    <th>컬렉션</th>
    <th>읽기(접근시간)</th>
    <th>추가 / 삭제</th>
    <th>비고</th>
  </tr>
  <tr>
    <td style="text-align:center">ArrayList</td>
    <td style="text-align:center">빠르다</td>
    <td style="text-align:center">느리다</td>
    <td>순차적인 추가, 삭제는 더 빠름.<br>비효율적인 메모리 사용</td>
  </tr>
  <tr>
    <td style="text-align:center">LinkedList</td>
    <td style="text-align:center">느리다</td>
    <td style="text-align:center">빠르다</td>
    <td>데이터가 많아질수록 접근성이 떨어짐</td>
  </tr>
</table>

**다루고자 하는 데이터의 개수가 변하지 않는 경우라면, ArrayList가 최상의 선택이 되겠지만, 데이터 개수의 변경이 잦다면 LinkedList를 사용하는 것이 더 나은 선택이 될 것이다.**
