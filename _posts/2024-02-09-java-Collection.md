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

<br>

## List

제일 먼저 리스트(List)에 대해 다뤄보겠다.

리스트는 <b>순서가 있고, 중복을 허용</b>한다. 순서가 있다는 것은 <b>인덱스가 있다는 것</b>이다. 즉, 리스트는 배열과 유사하지만 동적이다.

리스트의 구현 클래스로는 ArrayList, LinkedList, Vector 등이 있다.

<br>

간략하게 보자면, ArrayList는 실제 배열로 구현이 되어 있다. 초기에는 작은 크기의 배열로 출발을 한다. 기본적으로 자바에서는 배열의 크기(size)를 초기에 설정하고 사용한다. 그리고 실제 사이즈는 0번부터 시작하는 것으로 관리하였다. (실습 때)

size가 10인 배열을 만들어 두고, 차근차근 채워나가서 절반 정도 채웠다고 하면 ArrayList가 알아서 배열의 크기를 두 배 정도 늘려준다. 또, 늘린 크기의 배열이 절반 정도 찼다고 하면 두 배 정도 늘린다. 이런 식으로 알아서 크기를 늘려주고 있는 것이다. 반대로 줄어들기도 한다. 메모리 관리를 위해서 원소의 개수가 줄어들면 전체 길이도 줄어드는 것이다.

사실 ArrayList는 길이가 늘어날 때 50%만 늘어난다. (1.5배씩) 그리고 Vector의 경우에도 배열로 되어 있는데, 항상 100%씩 늘어난다. (2배씩)

즉, 크기가 고정되어 있는 배열이 새로 크기를 늘리거나 줄여서 배열을 만들어 복사하고 있는 것이다.

<br>

LinkedList는 조금 다르다. 각각의 노드들이 링크와 링크로 연결되어 있다. 하나의 노드에 있어서 두 개의 저장공간이 있다고 할 때, 앞에는 실제 값이 들어가고 뒤에는 그 다음 노드의 참조값이 들어간다. 노드가 꼬리에 꼬리를 물고 쭉 이어지는 구성으로 되어 있는 것이다.

그래서 새로 값을 추가하겠다고 한다면, 새로 노드를 만들어서 참조값을 이어주면 된다.

<br>

즉, List의 특징들은 다음과 같다.

* 내부적으로 배열을 이용하여 데이터를 관리
* 배열과 다르게 크기가 유동적으로 변함 (동적 자료구조)
* 배열을 다루는 것과 유사하게 사용할 수 있음

List의 주요 메서드는 다음과 같다. (이전에 본 Collection의 일반적인 메서드들과 비교하며 보겠다.)

<table>
    <tr>
        <th>분류</th>
        <th>Collection</th>
        <th>List</th>
    </th>
    <tr>
        <th>추가</th>
        <td>add(E e),<br>addAll(Collection&#60;? extends E&#62; c)</td>
        <td>add(int index, E element),<br>addAll(int index, Collection&#60;? extends E&#62; c)</td>
    </tr>
    <tr>
        <th>조회</th>
        <td>contains(Object o),<br>containsAll(Collection&#60;?&#62; c),<br>equals(),<br>isEmpty(),<br>iterator(),<br>size()</td>
        <td>get(int index),<br>indexOf(Object o),<br>lastIndexOf(Object o),<br>listIterator()</td>
    </tr>
    <tr>
        <th>삭제</th>
        <td>clear(),<br>removeAll(Collection&#60;?&#62; c)<br>retainAll(Collection&#60;?&#62; c)</td>
        <td>remove(int index)</td>
    </tr>
    <tr>
        <th>수정</th>
        <td></td>
        <td>set(int index, E element)</td>
    </tr>
    <tr>
        <th>기타</th>
        <td>toArray()</td>
        <td>subList(int fromIndex, int toIndex)</td>
    </tr>
</table>

<br>

위 주요 메서드들을 코드를 통해 보겠다.

```java
import java.util.ArrayList;
import java.util.List;

public class ListTest1 {
    public static void main(String[] args) {
    	// List
    	// 1. 순서가 있다.
    	// 2. 중복이 허용된다.
    	
    	List<String> names = new ArrayList<String>();
    	
    	// 원소 추가
    	names.add("Jang");
    	names.add("Kim");
    	names.add("Yoon");
    	names.add("Hong");
    	names.add("Kim"); // 중복
    	
    	System.out.println(names);
    	
    	// 비어있는지 체크
    	System.out.println(names.isEmpty());
    	
    	// 삭제
    	// 1. 인덱스를 이용한 삭제
    	names.remove(0);
    	System.out.println(names);
    	
    	// 2. 값을 이용한 삭제
    	names.remove("Yoon");
    	System.out.println(names);
    	
    	// 3. 전부 삭제
//    	names.clear();
//    	System.out.println(names);
//    	System.out.println(names.isEmpty());
    	
    	// 4. 중복된 값을
    	names.remove("Kim");
    	System.out.println(names);
    	
    	
    	// 삭제할 때 주의할 점!
    	names.clear();
    	
    	names.add("학생1");
    	names.add("학생1");
    	names.add("학생2");
    	System.out.println(names);
    	// 학생1을 다 삭제하고 싶다.
    	
    	// 삭제시, 리스트 크기도 바뀌고.. 각 원소들의 index도 바뀐다. - IndexOutOfBoundsException 발생
//    	for(int i = 0; i < names.size(); i++) {
//    		if(names.get(i).equals("학생1"))
//    			names.remove(i);
//    	}
//    	System.out.println(names);
    	
    	for(int i = names.size() - 1; i >= 0; i--) {
    		if(names.get(i).equals("학생1"))
    			names.remove(i);
    	}
    	System.out.println(names);
    	
    }
}
```

위 코드의 출력값은 다음과 같다.

```
[Jang, Kim, Yoon, Hong, Kim]
false
[Kim, Yoon, Hong, Kim]
[Kim, Hong, Kim]
[Hong, Kim]
[학생1, 학생1, 학생2]
[학생2]
```

ArrayList에서 삭제 메서드를 수행할 때, 인덱스로 접근하는 것에서 주의를 기울여야 한다. 삭제를 할 때 리스트 크기도 바뀌고, 각 원소들의 index도 바뀌기 때문이다. 위 코드에서 주석 처리된 삭제 연산식을 실행하면 IndexOutOfBoundsException이 발생한다.

위 상황을 해결하기 위해서는 거꾸로 접근하면 된다. 인덱스의 마지막에서 0까지 삭제를 수행하는 것이다.

<br>

이번에는 ListTest2 코드를 보겠다.

같은 List 인터페이스에 속해 있더라도, 구현체에 따라서 성능에 차이가 있다.

```java
package test01_list;

import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Vector;

public class ListTest2_비교 {
	public static void main(String[] args) {
		List<Object> al = new ArrayList<Object>();
		List<Object> ll = new LinkedList<Object>();
		List<Object> v = new Vector<Object>();
		
		/*
		    Vector와 ArrayList 모두 내부적으로 배열로 구현되어 있으나
		    Vector => 크기 2배씩 증가
		    ArrayList => 크기 1.5배씩 증가.
		*/

		test1("순차적 추가 -  ArrayList -", al);
		test1("순차적 추가 - LinkedList -", ll);
		test1("순차적 추가 -   Vector   -", v);

		test2("중간에 추가 -  ArrayList -", al);
		test2("중간에 추가 - LinkedList -", ll);
		test2("중간에 추가 -   Vector   -", v);

		test3("데이터 조회 -  ArrayList -", al);
		test3("데이터 조회 - LinkedList -", ll);
		test3("데이터 조회 -   Vector   -", v);
	}

	public static void test1(String testname, List<Object> list) {
		long start = System.nanoTime(); // 시작 시간
		for (int i = 0; i < 50000; i++) {
			list.add(new String("Hello")); // 뒤에다가 추가
		}
		long end = System.nanoTime(); // 끝 시간
		System.out.printf("%s \t 소요시간: %15d ns. \n", testname, end - start);
	}

	public static void test2(String testname, List<Object> list) {
		long start = System.nanoTime(); // 시작 시간
		for (int i = 0; i < 50000; i++) {
			list.add(0, new String("Hello")); // 맨앞에다가 추가(중간에 추가)
		}
		long end = System.nanoTime(); // 끝 시간
		System.out.printf("%s \t 소요시간: %15d ns. \n", testname, end - start);
	}

	public static void test3(String testname, List<Object> list) {
		long start = System.nanoTime(); // 시작 시간
		// 리스트에 있는 모든 원소 조회
		for (int i = 0; i < list.size(); i++) {
			list.get(i);
		}
		long end = System.nanoTime(); // 끝 시간
		System.out.printf("%s \t 소요시간: %15d ns. \n", testname, end - start);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
순차적 추가 -  ArrayList - 	 소요시간:         2048375 ns. 
순차적 추가 - LinkedList - 	 소요시간:         1569875 ns. 
순차적 추가 -   Vector   - 	 소요시간:         1113125 ns. 
중간에 추가 -  ArrayList - 	 소요시간:       355651792 ns. 
중간에 추가 - LinkedList - 	 소요시간:         2295417 ns. 
중간에 추가 -   Vector   - 	 소요시간:       351378958 ns. 
데이터 조회 -  ArrayList - 	 소요시간:         2431042 ns. 
데이터 조회 - LinkedList - 	 소요시간:      4442079542 ns. 
데이터 조회 -   Vector   - 	 소요시간:        14436417 ns.
```

위 코드에서 '순차적 추가'란 뒤에 계속 넣겠다는 것이다. '중간에 추가'한다는 것은 배열의 중간에 값을 넣겠다는 것이다. 증간에 추가를 하면 배열의 경우 일일이 다 뒤로 당겨줘야 한다. 그런데 LinkedList의 경우 참조로 연결되어 있기 때문에, 중간에 추가하고 싶으면 중간에 참조 링크 하나만 끊고 변경하면 된다. 이런 점에서 성능에 차이가 오는 것이다.

위 출력 결과를 보면, 순차적 추가를 할 때에는 Vector가 제일 빠르다. 그리고 중간에 추가할 때에는 ArrayList가 제일 느리고 LinkedList가 제일 빠르다. 데이터를 조회할 때에는 ArrayList가 제일 빠르고 LinkedList가 제일 느리다. LinkedList에서는 노드들이 일직선상에 놓여있지 않기 때문이다.

LinkedList의 경우 제일 첫 번째 노드만 기억하고 있다. 쭉 모든 노드들이 연결되어 있다고 할 때, 제일 첫 번째 노드만 기억하고 있는 것이다. 그래서 어떤 노드를 찾으려면 첫 번째 노드부터 타고 들어가야 한다.

이렇게 같은 List라 하더라도 성능에 차이가 난다.

<br>

만약 ArrayList를 구현해서 사용하고 싶다면 다음과 같이 하면 된다.

```java
// 문자열을 저장할 List, 구현체는 ArrayList
List<String> names = new ArrayList<>();

// 추가
names.add("Kim");
names.add("Hong");
names.add("Kim");
names.add("Jang");
names.add(0, "Yoon");   // 맨 앞에 추가하겠다는 것

System.out.println(names);
```

위 코드의 출력 결과는 다음과 같다.

```
[Yoon, Kim, Hong, Kim, Jang]
```

<br>

Array의 특징은 다음과 같다.

* 같은 타입의 데이터를 묶어 사용하는 자료구조
* 접근 속도가 빠름
* 크기를 변경할 수 없어 추가 데이터를 넣을 때, 새로운 배열을 만들고 복사함
* 데이터 삭제 시, 인덱스를 재조정하기 위해 전체 데이터를 옮겨야 함
* ArrayList 역시 Array를 활용하므로 이 같은 특징을 가지고 있음

<br>

ArrayList의 주요 메서드들은 다음과 같다.

* add(E e) : 데이터 입력
* get(int index) : 데이터 추출
* size() : 입력된 데이터의 크기 반환
* remove(int i) : 특정한 데이터를 삭제
* remove(Object o) : 특정한 데이터를 삭제
* clear() : 모든 데이터 삭제
* contains(Object o) : 특정 객체가 포함되어 있는지 체크
* isEmpty() : 비어있는지 체크(true, false)
* addAll(Collection c) : 기존 등록된 컬렉션 데이터 입력
* iterator() : iterator 인터페이스 객체 반환

<br>

LinkedList의 특징은 다음과 같다.

* 각 요소를 Node로 정의하고 Node는 다음 요소의 참조 값과 데이터로 구성됨
* 각 요소가 다음 요소의 링크 정보를 가지며 연속적으로 구성될 필요가 없음

<br>

## Set

Set은 List와 다르게 <b>순서가 보장되지 않고, 중복을 허용하지 않는다.</b>

List의 중복된 데이터를 제거하는 수단으로 사용될 수 있다. 순서가 없기 때문에 정렬하려면 별도의 처리가 필요하다. (리스트를 만들어서 정렬하는 등의 작업이 필요하다.)

구현 클래스로는 HashSet, TreeSet이 있다. 그런데 기본적으로 HashSet을 사용할 것이다. 이게 좀 더 성능이 좋다.

* HashSet - 내부적으로는 HashTable로 중복 검사를 하고 있다.

모든 Object에는 hashCode() 메서드가 있는데, 이것을 생각하면 된다. 이것으로 내부적으로 중복을 제거하고 있는 것이다.

* TreeSet - 레드블랙트리(Red-Black Tree)로 관리를 하고 있다.

구현까지 깊게 살펴볼 필요는 없겠다.

<br>

대표적인 구현체인 HashSet을 예시 코드로 알아보겠다.

```java
package test02_set;

import java.util.HashSet;
import java.util.Set;

public class SetTest1 {
    public static void main(String[] args) {
        // Set
    	// - Interface
    	// 1. 집합
    	// 2. 중복을 허용하지 않음
    	// 3. 순서가 보장되지 않는다.
    	
    	// HashSet : Class
    	// - Hashtable, 성능면에서 우수하다고 알려져 있음.
    	
    	Set<String> set = new HashSet<>();
    	
    	set.add("Park");
    	set.add("Kim");
    	set.add("Yoon");
    	set.add("Jang");
    	set.add("Hong");
    	set.add("Park");
    	
    	System.out.println(set);
    	System.out.println(set.size());

    }
}  
```

위 SetTest1의 실행 결과는 다음과 같다.

```
[Hong, Jang, Kim, Park, Yoon]
5
```

보면, Park를 두 번 add 했다. 그런데 출력 결과에는 Park가 한 번밖에 안 나온다. 그리고 순서대로 넣었지만 출력물을 보면 넣은 순서가 보장되지 않는 것을 볼 수 있다.

또, Set의 사이즈를 찍어보면 5가 나온다.

<br>

이번에는 Set을 또 다른 측면에서 테스트 해보겠다.

Person 클래스를 생성해서 Person의 집합을 만들 것이다.

```java
package test02_set;

public class Person {
    String name;
    String id; // 주민번호
    
    public Person(String name, String id) {
        super();
        this.name = name;
        this.id = id;
    }

    @Override
    public String toString() {
        return "Person [name=" + name + ", id=" + id + "]";
    }
}
```

그리고 아래에서 두 개의 사람 객체를 만들었다. 

```java
package test02_set;

import java.util.HashSet;
import java.util.Set;

public class SetTest2 {
    public static void main(String[] args) {
        Set<Person> set = new HashSet<Person>();
        // Set에서 중복을 판단하는 방법
        // 1. hashCode(); 일치시킨다.
        // 2. equals() 오버라이드.
        
        
        Person p1 = new Person("사람1", "111111");
        Person p2 = new Person("사람1", "111111");
        
        set.add(p1);
        set.add(p2);
        
        System.out.println(p1.hashCode()); // Object로부터 물려받은 계산 로직을 쓰는 것이 아니라..
        System.out.println(p2.hashCode());
        
        System.out.println("사람1".hashCode()); // String에서 오버라이드한 계산로직을 쓰겠다.
        System.out.println("사람1".hashCode());
        
        
        // 사람이 한 사람만? 아니면 두 사람?
        System.out.println(set);
    }
}
```

위 코드의 출력 결과는 다음과 같다.

```
1295083508
249155636
48857041
48857041
[Person [name=사람1, id=111111], Person [name=사람1, id=111111]]
```

p1과 p2는 다른 사람으로 출력된다. 한편, "사람1"과 "사람2"의 해시코드는 같게 나온다. 이건 또 왜 그런 것일까?

우선 "사람1"과 "사람2"의 해시코드가 같은 이유는 다음과 같다. <b>문자열은 문자열이 같다면 해시코드가 같게 나오는 것</b>이다. 반면, Person 객체는 Object로부터 물려받은 계산 로직을 쓴다. 

그런데 같은 key값을 넣었다면 같은 객체가 생성되게 하고 싶다. 이렇게 하려면, Person 객체를 손 봐야 한다.

Object의 메서드 말고, String에서 오버라이드한 계산 로직을 쓰고 싶은 것이다.

<br>

id가 일치한다면 같은 사람으로 보기 위해서, 일단 해시코드를 일치시켜주었다. <kbd>마우스 우클릭 &#62; Source &#62; Override/Implement Methods &#62; hashCode()</kbd>를 클릭하면 된다. 그러면 처음에는 이렇게 뜬다.

```java
@Override
public int hashCode() {
    return super.hashCode();
}
```

보면 Object의 hashCode()를 쓰고 있다. 부모 클래스의 hashCode()를 쓰고 있는 것이다. 이 코드를 다음과 같이 바꿨다.

```java
@Override
public int hashCode() {
    return id.hashCode();
}
```

이렇게 하여 id의 hashCode()를 반환하도록 한 것이다.

<br>

hashCode()만 오버라이드 해서는 안 되고, equals()도 해줘야 한다. 마찬가지 방법으로 equals() 메서드를 불러온다. 그러면 equals() 메서드는 처음에는 이렇게 되어있다.

```java
@Override
public boolean equals(Object obj) {
    return super.equals(obj);
}
```

equals() 메서드에 파라미터로 Object 객체가 들어올 텐데, 이것이 Person의 인스턴스일 때에만 검사를 할 수 있게 한다. 그리고 Person의 인스턴스라면, 형변환을 하고 id가 서로 같은지 검토하는 것이다.

그리고 만약에 인스턴스가 같지 않다면 false를 반환하게 한다. 그렇게 하기 위해서 다음과 같이 코드를 수정했다.

```java
@Override
public boolean equals(Object obj) {
    if(obj instanceof Person) {
        Person other = (Person) obj;
        return id.equals(other.id);
    }
    
    return false;
}
```

<br>

이렇게 Person 클래스에 메서드 오버라이드를 수행한 이후의 코드는 다음과 같다.

```java
package test02_set;

public class Person {
    String name;
    String id; // 주민번호
    
    public Person(String name, String id) {
        super();
        this.name = name;
        this.id = id;
    }

    @Override
    public String toString() {
        return "Person [name=" + name + ", id=" + id + "]";
    }
    
    // id가 일치한다면 같은 사람이다.
	@Override
	public int hashCode() {
		return id.hashCode();
	}

	@Override
	public boolean equals(Object obj) {
		if(obj instanceof Person) {
			Person other = (Person) obj;
			return id.equals(other.id);
		}
		
		return false;
	}
}
```

그리고 다시 위 SetTest2를 실행 시키면 출력 값이 다음과 같이 나온다.

```
1449589344
1449589344
48857041
48857041
[Person [name=사람1, id=111111]]
```

