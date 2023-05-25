---
layout: post
title: Collections의 메서드
description: Java의 정석 기초편 ch. 11 컬렉션 프레임웍
categories: Java
date: 2023-05-25 14:57:00 +09:00
---
Arrays가 배열과 관련된 메서드를 제공하는 것처럼, Collections는 컬렉션과 관련된 메서드를 제공한다. fill(), copy(), sort(), binarySearch() 등의 메서드는 두 클래스에 모두 포함되어 있으며 같은 기능을 한다. (이 메서드들은 Arrays에서 설명했다.)

(java.util.Collection은 인터페이스이고, java.util.Collections는 클래스이다.)

## 컬렉션의 동기화

멀티 쓰레드(multi-thread) 프로그래밍에서는 하나의 객체를 여러 쓰레드가 동시에 접근할 수 있기 때문에 데이터의 무결성(integrity)을 유지하기 위해서는 공유되는 객체에 동기화(synchronization)가 필요하다.

Vector와 Hashtable과 같은 구버전(JDK1.2 이전)의 클래스들은 자체적으로 동기화 처리가 되어 있는데, 멀티쓰레드 프로그래밍이 아닌 경우에는 불필요한 기능이 되어 성능을 떨어뜨리는 요인이 된다.

그래서 새로 추가된 ArrayList와 HashMap과 같은 컬렉션은 동기화를 자체적으로 처리하지 않고 필요한 경우에만 java.util.Collections클래스의 동기화 메서드를 이용해서 동기화처리가 가능하도록 변경하였다.

Collections클래스에는 다음과 같은 동기화 메서드를 제공하고 있으므로, 동기화가 필요할 때 해당하는 것을 사용하면 된다.

```java
static Collection synchronizedCollection(Collection c)
static List synchronizedList(List list)
static Set synchronizedSet(Set s)
static Map synchronizedMap(Map m)
static SortedSet synchronizedSortedSet(SortedSet s)
static SortedMap synchronizedSortedMap(SortedMap m)
```

이들을 사용하는 방법은 다음과 같다.

```java
List syncList = Collections.synchronizedList(new ArrayList(...));
```

## 변경불가 컬렉션 만들기

컬렉션에 저장된 데이터를 보호하기 위해서 컬렉션을 변경할 수 없게, 즉 읽기 전용으로 만들어야할 때가 있다. 주로 멀티 쓰레드 프로그래밍에서 여러 쓰레드가 하나의 컬렉션을 공유하다보면 데이터가 손상될 수 있는데, 이를 방지하려면 아래의 메서드들을 이용하면 된다.

```java
static Collection unmodifiableCollection(Collection c)
static List unmodifiableList(List list)
static Set unmodifiableSet(Set s)
static Map unmodifiableMap(Map m)
static NavigableSet unmodifiableNavigableSet(NavigableSet s)
static SortedSet unmodifiableSortedSet(SortedSet s)
static NavigableMap unmodifiableNavigableMap(NavigableMap m)
static SortedMap unmodifiableSortedMap(SortedMap m)
```


## 싱글톤 컬렉션 만들기

단 하나의 객체만을 저장하는 컬렉션을 만들어야 하는 경우가 있다. 이럴 때는 아래의 메서드를 사용하면 된다.

```java
static List singletonList(Object o)
static Set singleton(Object o)      // singletonSet이 아님에 주의
static Map singletonMap(Object key, Object value)
```

매개변수로 저장할 요소를 지정하면, 해당 요소를 저장하는 컬렉션을 반환한다. 그리고 반환된 컬렉션은 변경할 수 없다.


## 한 종류의 객체만 저장하는 컬렉션 만들기

컬렉션에 모든 종류의 객체를 저장할 수 있다는 것은 장점이기도 하고 단점이기도 하다. 대부분의 경우 한 종류의 객체를 저장하며, 컬렉션에 지정된 종류의 객체만 저장할 수 있도록 제한하고 싶을 때 아래의 메서드를 사용한다.

```java
static Collection checkedCollection(Collection c, Class type)
static List checkedList(List list, Class type)
static Set checkedSet(Set s, Class type)
static Map checkedMap(Map m, Class keyType, Class valueType)
static Queue checkedQueue(Queue queue, Class type)
static NavigableSet checkedNavigableSet(NavigableSet s, Class type)
static SortedSet checkedSortedSet(SortedSet s, Class type)
static NavigableMap checkedNavigableMap(NavigableMap m, Class keyType, Class valueType)
static SortedMap checkedSortedMap(SortedMap m, Class keyType, Class valueType)
```

사용 방법은 다음과 같이 두 번째 매개변수에 저장할 객체의 클래스를 지정하면 된다.

```java
List list = new ArrayList();
List checkedList = checkedList(list, String.class);   // String만 저장 가능
checkedList.add("abc");             // OK.
checkedList.add(new Integer(3));    // ERROR. ClassCastException 발생
```

컬렉션에 저장할 요소의 타입을 제한하는 것은 지네릭스(generics)로 간단히 처리할 수 있는데도 이런 메서드들을 제공하는 이유는 호환성 때문이다. 지네릭스는 JDK1.5부터 도입된 기능이므로 JDK1.5 이전에 작성된 코드를 사용할 때는 이 메서드들이 필요할 수 있다.


## Collections 예제

```java
import java.util.*;
import static java.util.Collections.*;

class Ex11_19 {
	public static void main(String[] args) {
		List list = new ArrayList();
		System.out.println(list);

		addAll(list, 1,2,3,4,5); 
		System.out.println(list);
		
		rotate(list, 2);  // 오른쪽으로 두 칸씩 이동 
		System.out.println(list);

		swap(list, 0, 2); // 첫 번째와 세 번째를 교환(swap)
		System.out.println(list);

		shuffle(list);    // 저장된 요소의 위치를 임의로 변경 
		System.out.println(list);

		sort(list, reverseOrder()); // 역순 정렬 reverse(list);와 동일 
		System.out.println(list);
		
		sort(list);       // 정렬 
		System.out.println(list);
 
		int idx = binarySearch(list, 3);  // 3이 저장된 위치(index)를 반환
		System.out.println("index of 3 = " + idx);
		
		System.out.println("max="+max(list));
		System.out.println("min="+min(list));
		System.out.println("min="+max(list, reverseOrder()));

		fill(list, 9); // list를 9로 채운다.
		System.out.println("list="+list);

		// list와 같은 크기의 새로운 list를 생성하고 2로 채운다. 단, 결과는 변경불가
		List newList = nCopies(list.size(), 2); 
		System.out.println("newList="+newList);

		System.out.println(disjoint(list, newList)); // 공통요소가 없으면 true

		copy(list, newList); 
		System.out.println("newList="+newList);
		System.out.println("list="+list);

		replaceAll(list, 2, 1); 
		System.out.println("list="+list);

		Enumeration e = enumeration(list);
		ArrayList list2 = list(e); 

		System.out.println("list2="+list2);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
[]
[1, 2, 3, 4, 5]
[4, 5, 1, 2, 3]
[1, 5, 4, 2, 3]
[2, 5, 3, 4, 1]
[5, 4, 3, 2, 1]
[1, 2, 3, 4, 5]
index of 3 = 2
max=5
min=1
min=1
list=[9, 9, 9, 9, 9]
newList=[2, 2, 2, 2, 2]
true
newList=[2, 2, 2, 2, 2]
list=[2, 2, 2, 2, 2]
list=[1, 1, 1, 1, 1]
list2=[1, 1, 1, 1, 1]
```


## 컬렉션 클래스 정리 & 요약

지금까지 소개한 컬렉션 클래스의 특징과 관계를 그림으로 정리해보았다. 각 컬렉션 클래스마다 장단점이 있으므로 구현 원리와 특징을 잘 이해해서 상황에 가장 적합한 것을 선택해야 하겠다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f502529d-dcb3-4637-ac73-aaf8e38f0c81" width="1000" title="컬렉션 클래스 관계도" alt="[자바의 정석 - 기초편] ch11-52~56 Collections클래스, 컬렉션 클레스 요약">

<table>
  <tr>
    <th>컬렉션</th>
    <th>특징</th>
  </tr>
  <tr>
    <td>ArrayList</td>
    <td>배열기반. 데이터의 추가와 삭제에 불리, 순차적인 추가삭제는 제일 빠름. 임의의 요소에 대한 접근성(accessibility)이 뛰어남.</td>
  </tr>
  <tr>
    <td>LinkedList</td>
    <td>연결기반. 데이터의 추가와 삭제에 유리. 임의의 요소에 대한 접근성이 좋지 않다.</td>
  </tr>
  <tr>
    <td>HashMap</td>
    <td>배열과 연결이 결합된 상태. 추가, 삭제, 검색, 접근성이 모두 뛰어남. 검색에는 최고 성능을 보인다.</td>
  </tr>
  <tr>
    <td>TreeMap</td>
    <td>연결기반. 정렬과 검색(특히 범위검색)에 적합. 검색 성능은 HashMap보다 떨어짐.</td>
  </tr>
  <tr>
    <td>Stack</td>
    <td>Vector을 상속받아 구현</td>
  </tr>
  <tr>
    <td>Queue</td>
    <td>LinkedList가 Queue인터페이스를 구현</td>
  </tr>
  <tr>
    <td>Properties</td>
    <td>Hashtable을 상속받아 구현</td>
  </tr>
  <tr>
    <td>HashSet</td>
    <td>HashMap을 이용해서 구현</td>
  </tr>
  <tr>
    <td>TreeSet</td>
    <td>TreeMap을 이용해서 구현</td>
  </tr>
  <tr>
    <td>LinkedHashMap<br>LinkedHashSet</td>
    <td>HashMap과 HashSet에 저장순서 유지기능을 추가</td>
  </tr>
</table>
