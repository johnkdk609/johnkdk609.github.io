---
layout: post
title: Collection Framework
categories: Java
date: 2024-02-09 23:21:00 +0900
---
이번에는 Collection에 대해 알아보겠다.

Collection은 자바에서 자료구조를 다루는 것들을 지칭하는 것이다. 인터페이스, 클래스들의 모임이라고 할 수 있다.

대표적으로 다음과 같은 것들이 있다.

* List
* Set
* Map
* Queue
* Stack

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