---
layout: post
title: Collection Framework
categories: Java
date: 2024-02-09 23:21:00 +0900
---
이번에는 Collection에 대해 알아보겠다.

Collection은 자바에서 자료구조를 다루는 것들을 지칭하는 것이다. 인터페이스, 클래스들의 모임이라고 할 수 있다.

대표적으로 다음과 같은 것들이 있다.

* <b>List</b>
* <b>Set</b>
* <b>Map</b>
* <b>Queue</b>
* <b>Stack</b>

<br>

Collection이라는 말 자체가 무언가를 모아놨다는 뜻이다. '자료구조'라는 것이 여러 객체들이 모아져 있다는 뜻이다. 객체들을 한 곳에 모아놓고 편리하게 사용할 수 있는 환경을 제공한다.

자료구조(Data Structure)은 크게 2가지로 나눈다.

* <b>정적 자료구조 (Static Data Structure)</b>
    * 고정된 크기의 자료구조
    * 배열이 대표적인 정적 자료구조
    * 선언 시 크기를 명시하면 바꿀 수 없음

* <b>동적 자료구조 (Dynamic Data Structure)</b>
    * 요소의 개수에 따라 자료구조의 크기가 동적으로 증가하거나 감소
    * 리스트, 스택, 큐 등

<br>

자료구조들의 종류는 결국 어떤 구조에서 얼마나 빨리 원하는 데이터를 찾는가에 따라 결정된다. 자료구조는 쓰임새가 다르다. 우리가 해결하고자 하는 문제마다 적합한 자료구조가 있다. 어떤 것을 선택할지 상당히 중요하다고 할 수 있다.

자료구조를 크게 비교해보자면 다음과 같은 항목들로 체크해볼 수 있다.

* 순서를 유지할 것인가?
* 중복을 허용할 것인가?
* 다른 자료구조들에 비해서 어떤 단점과 장점을 가지고 있는가?

비교를 할 수 있어야 한다. List와 Set을 비교할 수 있어야 하고, List와 Map을 비교할 수 있어야 하는 등... 쓰임새가 다르기 때문이다.

분류도를 보면 다음과 같다.

* Collection
    * List
        * ArrayList
        * LinkedList
        * Vector
        * Stack
    * Set
        * HashSet
        * TreeSet
    * Queue
        * LinkedList
    * Map
        * HashMap
        * TreeMap
        * HashTable
        * Properties

위 분류에서, List, Set, Queue, Map 단계들은 인터페이스이다. 인터페이스는 '완벽하게 추상화된 설계도'이다. 그래서 추상 메서드들로 이루어져 있다. (public abstract) 인터페이스의 경우 default method, static method와 같이 구현부가 있을 수 있기는 하다.

아무튼 이 상위 레벨들은 인터페이스로만 관리를 하는 것이다. 예를 들어 List를 보면, List를 구현하는 방법이 여러 가지가 있는데, 구현 방법에 따라 ArrayList, LinkedList, Vector, Stack 등이 있는 것이다. 즉, 위 분류도에서 인터페이스 계층 아래 분위의 것들은 클래스이다.

때에 따라 List인지, Queue인지를 선택을 하지만, 같은 List라도 때에 따라서 어떨 때에는 ArrayList를 쓸지 LinkedList를 쓸지 고민을 해야 하는 것이다.

<br>

이러한 클래스들은 java.util 패키지에 모여 있다. java.util 패키지에서는 다수의 데이터를 쉽게 처리하는 방법을 제공한다.

Collection Framework 핵심 interface 는 다음과 같다.

<table>
    <tr>
        <th>interface</th>
        <th>특징</th>
    </tr>
    <tr>
        <th>List</th>
        <td>순서가 있는 데이터의 집합. 데이터의 중복 가능<br>ex&#41; 일렬로 줄 서기<br>ArrayList, LinkedList, Vector</td>
    </tr>
    <tr>
        <th>Set</th>
        <td>순서를 유지하지 않는 데이터의 집합. 데이터 중복 불가<br>ex&#41; 로또 당첨번호<br>HashSet, TreeSet...</td>
    </tr>
    <tr>
        <th>Map</th>
        <td>key와 value의 쌍으로 데이터를 관리하는 집합. key의 중복 불가, value는 중복 가능<br>ex&#41; 속성 - 값<br>HashMap, TreeMap</td>
    </tr>
    <tr>
        <th>Queue</th>
        <td>지하철, 버스를 탈 때 대기줄과 같이 들어온 순서대로 나가는 자료구조<br>LinkedList</td>
    </tr>
</table>

크게 4가지의 인터페이스가 있다. 대강 살펴보자면 다음과 같다. 우선 List는 순서가 있으면서 데이터의 중복이 가능한 것이다. <u>배열인데 크기가 변경 가능한 것</u>이라고 보면 된다. 배열처럼 순서가 있기 때문에 <u>인덱스(Index)로 접근</u>할 수 있다. 0번 인덱스부터 접근할 수 있고, List의 구현체로는 ArrayList, LinkedList, Vector 등이 있다.

다음으로 Set은 말 그대로 집합이다. 기본적으로 <u>순서를 유지하지 않고, 데이터의 중복이 불가능</u>하다. 구현체로는 HashSet, TreeSet 등이 있다.

Map은 <u>키-밸류(key-value) 쌍</u>으로 되어 있는 것이다. 키-밸류 한 쌍이 한 단위로 들어가 있는 것이다. <u>key는 중복이 불가한데, value는 중복이 가능</u>하다. 데이터를 찾을 때 key를 기준으로 찾기 때문이다. 구현체로는 HashMap, TreeMap 등이 있다.

마지막으로 Queue는 <u>선입선출(FIFO, First In First Out) 구조</u>라고 생각하면 된다. 구현체로는 LinkedList가 있다. 보면 List의 구현체로도 LinkedList가 있다. LinkedList는 클래스인데, 같은 클래스라 하더라도 List 측면에서 바라보면 이것은 List가 되는 것이고, Queue로 바라본다면 Queue가 되는 것이다. <b>다형성의 개념이 적용</b>되는 것이라 할 수 있는 것이다. 인터페이스는 약속이기 때문에, '~ 한 기능을 하면 List로 보겠다' 혹은 '~ 한 기능을 하면 Queue로 보겠다'는 것인데, LinkedList에는 그 기능들이 다 들어가 있는 것이다. 그런데 그 중에서 일부만 바라보는 것이다.

<br>

Collection 인터페이스에서 기본적으로 요구하는 것들이 여러 가지가 있다. 아래와 같은 기능들이 있으면 Collection의 Object라고 할 수 있겠다.

<table>
    <tr>
        <th>분류</th>
        <th>Collection</th>
    </th>
    <tr>
        <th>추가</th>
        <td>add(E e),<br>addAll(Collection&#60;? extends E&#62; c)</td>
    </tr>
    <tr>
        <th>조회</th>
        <td>contains(Object o),<br>containsAll(Collection&#60;?&#62; c),<br>equals(),<br>isEmpty(),<br>iterator(),<br>size()</td>
    </tr>
    <tr>
        <th>삭제</th>
        <td>clear(),<br>removeAll(Collection&#60;?&#62; c)<br>retainAll(Collection&#60;?&#62; c)</td>
    </tr>
    <tr>
        <th>수정</th>
        <td></td>
    </tr>
    <tr>
        <th>기타</th>
        <td>toArray()</td>
    </tr>
</table>

가령 위 표를 봤을 때, 원소를 추가(add)하고 제거(clear)하는 기능이 있다. 이것들은 구체적으로 각각의 Collection을 보면서 살펴볼 것이다.