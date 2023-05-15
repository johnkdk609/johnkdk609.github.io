---
layout: post
title: Collection, List, Set, Map 인터페이스
description: Java의 정석 기초편 ch. 11 컬렉션 프레임웍
categories: Java
date: 2023-05-15 17:35:00 +09:00
---
List와 Set의 조상인 Collection인터페이스에는 다음과 같은 메서드들이 정의되어 있다.

<table>
  <tr>
    <th>메서드</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>boolean add(Object o)<br>boolean addAll(Collection c)</td>
    <td>지정된 객체(o) 또는 Collection(c)의 객체들을 Collection에 추가한다.</td>
  </tr>
  <tr>
    <td>void clear()</td>
    <td>Collection의 모든 객체들을 삭제한다.</td>
  </tr>
  <tr>
    <td>boolean contains(Object o)<br>boolean containsAll(Collection c)</td>
    <td>지정된 객체(o) 또는 Collection의 객체들이 Collection에 포함되어 있는지 확인한다.</td>
  </tr>
  <tr>
    <td>boolean equals(Object o)</td>
    <td>동일한 Collection인지 비교한다.</td>
  </tr>
  <tr>
    <td>int hashCode()</td>
    <td>Collection의 hash code를 반환한다.</td>
  </tr>
  <tr>
    <td>boolean isEmpty()</td>
    <td>Collection이 비었는지 확인한다.</td>
  </tr>
  <tr>
    <td>Iterator iterator()</td>
    <td>Collection의 Iterator를 얻어서 반환한다.</td>
  </tr>
  <tr>
    <td>boolean remove(Object o)</td>
    <td>지정된 객체를 삭제한다.</td>
  </tr>
  <tr>
    <td>boolean removeAll(Collection c)</td>
    <td>지정된 Collection에 포함된 객체들을 삭제한다.</td>
  </tr>
  <tr>
    <td>boolean retainAll(Collection c)</td>
    <td>지정된 Collection에 포함된 객체만을 남기고 다른 객체들은 Collection에서 삭제한다.<br>이 작업으로 인해 Collection에 변화가 있으면 true를 그렇지 않으면 false를 반환한다.</td>
  </tr>
  <tr>
    <td>int size()</td>
    <td>Collection에 저장된 객체의 개수를 반환한다.</td>
  </tr>
  <tr>
    <td>Object[] toArray()</td>
    <td>Collection에 저장된 객체를 객체배열(Object[])로 반환한다.</td>
  </tr>
  <tr>
    <td>Object[] toArray(Object[] a)</td>
    <td>지정된 배열에 Collection의 객체를 저장해서 반환한다.</td>
  </tr>
</table>

(Iterator인터페이스는 컬렉션에 포함된 객체들에 접근할 수 있는 방법을 제공한다.)

Collection인터페이스는 **컬렉션 클래스에 저장된 데이터를 읽고, 추가하고 삭제하는 등 컬렉션을 다루는 데에 가장 기본적인 메서드들을 정의하고 있다.**

위의 표에서 반환 타입이 boolean인 메서드들은 작업을 성공하거나 사실이면 true를, 그렇지 않으면 false를 반환한다.

예를 들어 'boolean add(Object o)'를 사용해서 객체를 컬렉션에 추가할 때, 성공하면 true를, 실패하면 false를 반환한다. 'boolean isEmpty()'를 사용해서 컬렉션에 포함된 객체가 없으면, 즉 컬렉션이 비어있으면 true를, 그렇지 않으면 false를 반환한다. 

이외에도 JDK1.8부터 추가된 '람다(Lambda)와 스트림(Stream)'에 관련된 메서드들이 있다.


## List인터페이스

List인터페이스는 **중복을 허용하면서 저장 순서가 유지되는 컬렉션을 구현하는 데에 사용된다.**

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5098d837-285c-44c2-9ac1-d10da0fd1af4" width="500" title="List의 상속계층도" alt="List의 상속계층도">

List인터페이스에 정의된 메서드는 다음과 같다. Collection인터페이스로부터 상속받은 것들은 제외하였다.

<table>
  <tr>
    <th>메서드</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>void add(int index, Object element)<br>boolean addAll(int index, Collection c)</td>
    <td>지정된 위치(index)에 객체(element) 또는 컬렉션에 포함된 객체들을 추가한다.</td>
  </tr>
  <tr>
    <td>Object get(int index)</td>
    <td>지정된 위치(index)에 있는 객체를 반환한다.</td>
  </tr>
  <tr>
    <td>int indexOf(Object o)</td>
    <td>지정된 객체의 위치(index)를 반환한다.<br>(List의 첫 번째 요소부터 순방향으로 찾는다.)</td>
  </tr>
  <tr>
    <td>int lastIndexOf(Object o)</td>
    <td>지정된 객체의 위치(index)를 반환한다.<br>(List의 마지막 요소부터 역방향으로 찾는다.)</td>
  </tr>
  <tr>
    <td>ListIterator listIterator()<br>ListIterator listIterator(int index)</td>
    <td>List의 객체에 접근할 수 있는 ListIterator를 반환한다.</td>
  </tr>
  <tr>
    <td>Object remove(int index)</td>
    <td>지정된 위치(index)에 있는 객체를 삭제하고 삭제된 객체를 반환한다.</td>
  </tr>
  <tr>
    <td>Object set(int index, Object element)</td>
    <td>지정된 위치(index)에 객체(element)를 저장한다.</td>
  </tr>
  <tr>
    <td>void sort(Comparator c)</td>
    <td>지정된 비교자(comparator)로 List를 정렬한다.</td>
  </tr>
  <tr>
    <td>List subList(int fromIndex, int toIndex)</td>
    <td>지정된 범위(fromIndex부터 toIndex)에 있는 객체를 반환한다.</td>
  </tr>
</table>


## Set인터페이스

Set인터페이스는 **중복을 허용하지 않고 저장 순서가 유지되지 않는 컬렉션 클래스를 구현하는 데에 사용된다.** Set인터페이스를 구현한 클래스로는 HashSet, TreeSet 등이 있다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ffb86bf6-5112-4ecb-89a6-7ebae3811d68" width="340" title="Set의 상속계층도" alt="Set의 상속계층도">

Set인터페이스에는 다음과 같은 메서드들이 있는데, 모두 Collection인터페이스로부터 상속받은 것들이다.

<table>
  <tr>
    <th>메서드</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>boolean add(Object o)<br>boolean addAll(Collection c)</td>
    <td>지정된 객체(o) 또는 Collection의 객체들을 Collection에 추가한다.</td>
  </tr>
  <tr>
    <td>void clear()</td>
    <td>Collection의 모든 객체를 삭제한다.</td>
  </tr>
  <tr>
    <td>boolean contains(Object o)<br>boolean containsAll(Collection c)</td>
    <td>지정된 객체(o) 또는 Collection의 객체들이 Collection에 포함되어 있는지 확인한다.</td>
  </tr>
  <tr>
    <td>boolean equals(Object o)</td>
    <td>동일한 Collection인지 확인한다.</td>
  </tr>
  <tr>
    <td>int hashCode()</td>
    <td>Collection의 hash code를 반환한다.</td>
  </tr>
  <tr>
    <td>boolean isEmpty()</td>
    <td>Collection이 비어있는지 확인한다.</td>
  </tr>
  <tr>
    <td>Iterator iterator()</td>
    <td>Collection의 Iterator를 얻어서 반환한다.</td>
  </tr>
  <tr>
    <td>boolean remove(Object o)</td>
    <td>지정된 객체를 삭제한다.</td>
  </tr>
  <tr>
    <td>boolean removeAll(Collection c)</td>
    <td>지정된 Collection에 포함된 객체들을 삭제한다.</td>
  </tr>
  <tr>
    <td>boolean retainAll(Collection c)</td>
    <td>지정된 Collection에 포함된 객체만을 남기고 다른 객체들은 Collection에서 삭제한다.<br>이 작업으로 인해 Collection에 변화가 있으면 true를 그렇지 않으면 false를 반환한다.</td>
  </tr>
  <tr>
    <td>int size()</td>
    <td>Collection에 저장된 객체의 개수를 반환한다.</td>
  </tr>
  <tr>
    <td>Object[] toArray()</td>
    <td>Collection에 저장된 객체를 객체배열(Object[])로 반환한다.</td>
  </tr>
  <tr>
    <td>Object[] toArray(Object[] a)</td>
    <td>지정된 배열에 Collection의 객체를 저장해서 반환한다.</td>
  </tr>
</table>


## Map인터페이스

Map인터페이스는 **키(key)와 값(value)을 하나의 쌍으로 묶어서 저장하는 컬렉션 클래스를 구현하는 데 사용된다.** 키는 중복될 수 없지만 값은 중복을 허용한다. 기존에 저장된 데이터와 중복된 키와 값을 저장하면 기존의 값은 없어지고 마지막에 저장된 값이 남게 된다. Map인터페이스를 구현한 클래스로는 Hashtable, HashMap, LinkedHashMap, SortedMap, TreeMap 등이 있다. 

(Map이란 개념은 어떤 두 값을 연결한다는 의미에서 붙여진 이름이다.)

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c8fab08c-8b64-4690-859f-2bff1301bf6b" width="500" title="Map의 상속계층도" alt="Map의 상속계층도">

<table>
  <tr>
    <th>메서드</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>void clear()</td>
    <td>Map의 모든 객체를 삭제한다.</td>
  </tr>
  <tr>
    <td>boolean containsKey(Object key)</td>
    <td>지정된 key객체와 일치하는 Map의 key객체가 있는지 확인한다.</td>
  </tr>
  <tr>
    <td>boolean containsValue(Object value)</td>
    <td>지정된 value객체와 일치하는 Map의 value객체가 있는지 확인한다.</td>
  </tr>
  <tr>
    <td>Set entrySet()</td>
    <td>Map에 저장되어 있는 key-value쌍을 Map.Entry타입의 객체로 저장한 Set으로 반환한다.</td>
  </tr>
  <tr>
    <td>boolean equals(Object o)</td>
    <td>동일한 Map인지 비교한다.</td>
  </tr>
  <tr>
    <td>Object get(Object key)</td>
    <td>지정한 key객체에 대응하는 value객체를 찾아서 반환한다.</td>
  </tr>
  <tr>
    <td>int hashCode()</td>
    <td>해시코드를 반환한다.</td>
  </tr>
  <tr>
    <td>boolean isEmpty()</td>
    <td>Map이 비어있는지 확인한다.</td>
  </tr>
  <tr>
    <td>Set keySet()</td>
    <td>Map에 저장된 모든 key객체를 반환한다.</td>
  </tr>
  <tr>
    <td>Object put(Object key, Object value)</td>
    <td>Map에 value객체를 key객체에 연결(mapping)하여 저장한다.</td>
  </tr>
  <tr>
    <td>void putAll(Map t)</td>
    <td>지정된 Map의 모든 key-value쌍을 추가한다.</td>
  </tr>
  <tr>
    <td>Object remove(Object key)</td>
    <td>지정한 key객체와 일치하는 key-value객체를 삭제한다.</td>
  </tr>
  <tr>
    <td>int size()</td>
    <td>Map에 저장된 key-value쌍의 개수를 반환한다.</td>
  </tr><tr>
    <td>Collection values()</td>
    <td>Map에 저장된 모든 value객체를 반환한다.</td>
  </tr>
</table>

values()에서는 반환타입이 Collection이고, keySet()에서는 반환타입이 Set인 것에 주목해야 한다. Map인터페이스에서 값(value)은 중복을 허용하기 때문에 Collection타입으로 반환하고, 키(key)는 중복을 허용하지 않기 때문에 Set타입으로 반환한다. 
