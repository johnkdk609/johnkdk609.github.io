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
    </tr>
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
    </tr>
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

hashCode()와 equals()를 오버라이드 했기 때문에, p1과 p2는 같은 객체로 취급 받는다. 중복을 판단할 때 해시코드로 판단을 하기 때문이다. 이제 set에 하나만 들어 있는 것을 볼 수 있다.

<br>

## Map

Map의 경우 Key(키)와 Value(값)를 하나의 Entry로 묶어서 관리한다. <b>키-밸류 쌍</b>으로 되어 있다는 것이고, <b>순서가 없으며 키에 대한 중복은 없다.</b> 반면 Value는 중복될 수 있다.

Map의 장점으로는 빠른 속도가 있다.

구현 클래스로는 HashMap과 TreeMap이 있다.

<br>

예시 코드로 알아보겠다.

```java
package test03_map;

import java.util.HashMap;
import java.util.Map;

public class MapTest {
    public static void main(String[] args) {
        // Map
    	// - 사전과 같은 자료구조
    	// - 키 & 값의 쌍으로 저장
    	// - 키로 구별(중복 X), 값(중복 가능)
    	// - 키가 마치 set처럼... 순서가 보장되지는 않는다.
    	
    	Map<String, String> map = new HashMap<>();
    	
    	// 값을 넣을 때 put
    	map.put("5반", "Kim");
    	map.put("6반", "Lee");
    	map.put("7반", "Park");
    	map.put("8반", "Seo");
    	map.put("3반", "Heo");
    	map.put("4반", "Kim");
    	
    	System.out.println(map);
    	
    	System.out.println(map.get("3반"));
    	
    	map.put("6반", "Song");
    	System.out.println(map);
    	
    	// 똑같은 키에다가 새로운 값을 넣으면 대체가 됩니다.
    	
    	System.out.println(map.containsKey("4반"));
    	System.out.println(map.containsValue("Kim"));
    	
    	// 맵의 순회
    	// 1. keySet()
    	for(String key : map.keySet()) {
    		System.out.printf("%s : %s \n", key, map.get(key));
    	}
    	
    	// 2. entrySet()
    	for(Map.Entry<String, String> entry : map.entrySet()) {
    		System.out.println(entry.getKey() + " : " + entry.getValue());
    	}       
    }
}
```

위 코드의 출력 결과는 다음과 같다.

```
{8반=Seo, 7반=Park, 6반=Lee, 5반=Kim, 4반=Kim, 3반=Heo}
Heo
{8반=Seo, 7반=Park, 6반=Song, 5반=Kim, 4반=Kim, 3반=Heo}
true
true
8반 : Seo 
7반 : Park 
6반 : Song 
5반 : Kim 
4반 : Kim 
3반 : Heo 
8반 : Seo
7반 : Park
6반 : Song
5반 : Kim
4반 : Kim
3반 : Heo
```

출력 결과를 보면, 넣은 순서대로 나오지 않는다. 왜냐하면 키가 마치 Set처럼 순서가 보장되지 않기 때문이다.

그리고 똑같은 키에 새로운 값을 넣으면 값이 대체된다는 것을 알 수 있다.

또, containsKey() 와 containsValue() 메서드로 키나 밸류가 있는지 확인할 수 있었다.

맵의 순회의 경우, ```map.keySet()```를 활용할 수 있고 또 다른 방법으로는 entrySet()을 활용할 수 있다. keySet()이 key만 가져왔다면, entrySet()은 한꺼번에 가져온다. entrySet()을 사용할 때, 각각은 entry가 된다. 사용하려면 다음과 같이 하면 된다.

```java
for (Map.Entry<String, String> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " : " + entry.getValue());
}
```

<br>

Map도 Set과 비슷하다. 키를 구별할 때, Map의 key로 Person 클래스를 사용하는 예시 코드를 보자.

```java
package test03_map;

import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;

public class MapTest2 {
    public static void main(String[] args) {
        Map<Person, Integer> map = new HashMap<>();
        
        Person p1 = new Person("사람1", "111111");
        Person p2 = new Person("사람1", "111111");
        // hashCode, equals 오버라이드 해줘야 한다.
        
        
        map.put(p1, 100);
        map.put(p2, 98);
        
        // 사람이 한 사람만? 아니면 두 사람?
        System.out.println(map);
    }
}
```

위 코드의 실행 결과는 다음과 같다.

```
{Person [name=사람1, id=111111]=100, Person [name=사람1, id=111111]=98}
```

값이 두 개가 들어가 있다. key를 다른 key로 인식하고 있는 것으로 보인다.

똑같이 hashCode()와 equals()를 Override 해줘야 한다. Person 클래스를 다음과 같이 수정해야 하는 것이다.

```java
package test03_map;

public class Person {
    String name;
    String id;
    
    public Person(String name, String id) {
        super();
        this.name = name;
        this.id = id;
    }

    @Override
    public String toString() {
        return "Person [name=" + name + ", id=" + id + "]";
    }
    
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

이제 다시 위의 MapTest2를 실행하면 실행 결과는 다음과 같다.

```
{Person [name=사람1, id=111111]=98}
```

이번에는 하나만 들어가 있고, 이 값이 새로운 값으로 업데이트 되어 있다.

<br>

Map의 메서드들로는 다음과 같은 것들이 있다.

* V put(K key, V value) : 데이터 입력
* V get(Object key) : 데이터 추출
* V remove(K key) : 키의 값을 지우고 반환, 없다면 null을 반환
* boolean containsKey(Object key) : 특정한 key 포함 여부
* void putAll(Map&#60;K key, V value&#62; m) : 기존 컬렉션 데이터 추가
* set&#60;Map.Entry&#60;K, V&#62;&#62; entrySet() : key와 value 쌍을 표현하는 Map.Entry 집합을 반환


<br>

## Queue

Queue는 인터페이스이다. (인터페이스로는 객체 생성이 안 된다.) 구현체는 LinkedList를 사용한다.

큐는 FIFO(First In First Out) 즉, 가장 먼저 들어온 값이 가장 먼저 빠져나가는 자료구조이다. 리스트를 생각해보면, 값이 쭉 차있을 때, 앞에서도 제거할 수 있고 중간에서 제거할 수 있고 뒤에서 제거할 수도 있으며, 중간에 값을 넣을 수도 있다. 그런데 Queue는 제거는 앞에서만 가능하고, 추가는 뒤에서만 하도록 하는 것이다. 이 두 가지 기능만 쓰면 된다. 실제 링크드리스트는 다양한 메서드 기능을 구현할 수 있지만, Queue로 바라봤을 때에는 그 Queue로만 사용하는 것이다.

Queue의 메서드에는 다음과 같은 것들이 있다.

* boolean offer(E e) : 데이터를 추가
* E peek() : 가장 앞에 있는 데이터 조회
* E poll() : 가장 앞에 있는 데이터 빼내기
* boolean isEmpty() : 큐가 비어 있는지 여부

Queue는 순서가 있고 명칭이 있다. 제일 앞에 있는 것을 '머리(front)'라 하고, 제일 뒤에 있는 것을 '꼬리(rear)'라고 한다. offer()을 하게 되면 꼬리 뒤에 추가가 되어 이것이 새로운 꼬리가 된다. poll()로 제거하는 것은 머리이다. 그리고 머리는 그 다음 원소가 된다. peek()은 머리를 빼내지 않고 값만 확인하는 메서드이다.

<br>

예시 코드를 보겠다.

```java
package test04_queue;

import java.util.LinkedList;
import java.util.Queue;

public class QueueTest {
    public static void main(String[] args) {
    	// Queue
    	// - FIFO
    	// - Queue interface
    	// - LinkedList class
    	Queue<Integer> queue = new LinkedList<>();
    	
    	queue.offer(10);
    	
    	// queue에다가 순차적으로 값 집어넣기
    	for(int i=0; i<5; i++) {
    		queue.offer(i);
    	}
    	
    	// queue에서 값을 제거하기
    	while(!queue.isEmpty()) {
    		System.out.println(queue.poll());
    	}
        
    }
}
```

위 코드의 실행 결과는 다음과 같다.

```
10
0
1
2
3
4
```

넣은 순서대로 나오는 것을 알 수 있다.

<br>

## Stack

Queue가 FIFO였다면, Stack은 LIFO(Last In First Out)이다. 가장 나중에 들어온 값이 가장 먼저 빠져나가는 것이다.

가장 위에 있는 값(가장 나중에 들어간 값)을 'top'이라고 한다.

Stack의 메서드들은 다음과 같다.

* E push(E e) : 데이터를 추가
* E peek() : 가장 위에 있는 데이터 조회
* E pop() : 가장 위에 있는 데이터 빼내기
* boolean isEmpty() : 스택이 비어 있는지 여부

<br>

예시 코드로 알아보겠다.

```java
package test05_stack;

import java.util.Stack;

public class StackTest {
    public static void main(String[] args) {
    	// Stack
    	// - LIFO
    	// - Stack 클래스
    	Stack<Integer> stack = new Stack<>();
    	
    	for(int i=0; i<5; i++)
    		stack.push(i);
    	
    	// stack에서 값을 순서대로 꺼내기
    	// - 가장 나중에 들어간 값이, 가장 먼저 나온다..
    	while(!stack.isEmpty()) {
    		System.out.println(stack.pop());
    	}
    	
    }
}
```

위 코드의 출력 결과는 다음과 같다.

```
4
3
2
1
0
```

stack에 넣은 순서대로 나오는 것이 아닌, 마지막에 넣은 것부터 빠져나오는 것을 볼 수 있다.

<br>

## 정렬

정렬이란, 요소들을 특정 기준에 맞추어 오름차순 또는 내림차순으로 재배치하는 것이다.

Collection을 보면 순서가 있는 것이 있고, 없는 것이 있었다. 순서가 있는 Collection으로는 List가 있다. 

<b>순서를 가지는 Collection들만 정렬이 가능</b>하다. Collections의 <b>sort()</b>를 이용하여 정렬을 수행한다.

<br>

Collection Framework에서의 정렬은 두 가지 방법을 사용한다.

1. Comparable interface 구현
2. Comparator 활용

첫째, Comparable 인터페이스를 구현하면 정렬이 가능해진다.

```java
public interface Comparable<T> {
    public int compareTo(T o);
}
```

위 코드를 보면, compareTo()를 Override 해줘야 한다. 보면 int형을 반환하고 있다. <b>반환한 수가 음수나 0이면 자리를 유지하고, 양수이면 자리를 바꾸는 것</b>이다.

* 양수 : 자리 바꿈
* 음수 : 자리 유지
* 0 : 동일 위치

둘째, Comparator라는 객체를 만들어서 이것을 이용해도 된다.

<br>

코드를 통해 보겠다.

```java
package test06_sort;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class SortTest {
    public static void main(String[] args) {
        List<String> names = new ArrayList<String>();

        names.add("EEE");
        names.add("BBB");
        names.add("CCC");        
        names.add("AAA");
        names.add("DDD");
        
        System.out.println(names);
        
        // Collections.sort();
        Collections.sort(names);
        
        System.out.println(names);
    }
}
```

위 코드의 출력 결과는 다음과 같다.

```
[EEE, BBB, CCC, AAA, DDD]
[AAA, BBB, CCC, DDD, EEE]
```

Collections 클래스의 static method인 sort()를 호출하는 것이다. 출력 결과를 보면 정렬이 된 것을 확인할 수 있다.

<br>

두 번째 코드를 보겠다. 이번에는 이전에 생성한 Person클래스를 만들어서, Person의 리스트를 만들었다.

```java
package test07_comparable;

// 컬렉션 프레임워크에서 정렬가능하도록 만들려면
// Comparable 인터페이스
public class Person implements Comparable<Person> {
    String name;
    int age;
    
    
    public Person(String name, int age) {
        super();
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person [name=" + name + ", age=" + age + "]";
    }
    
    // 양수 -> 자리바꿈
    // 음수 -> 그대로
    // 0 -> 그대로

//	@Override
//	public int compareTo(Person o) {
//		// this(자기자신객체) o 객체를 비교해서 => 정수값을 반환.
//		
//		// 나이 순으로 정렬을 한다면?
//		// 기본적으로 오름차순으로 정렬한다면
//		// 3, 8, 11, 22
//		// 앞의 숫자가 작고, 뒤에 숫자가 크다.
//		// 앞 - 뒤 = 음수
//		
//		// 만약에 나이가 같다면.... 이름 순서로 하길 원함.
//		// 이름 : 문자열 => string의 compareTo를 사용하면 오름차순으로..
//		if(this.age == o.age) {
//			return this.name.compareTo(o.name);
//		}
//		
//		return this.age - o.age;
//	}
	
    @Override
	public int compareTo(Person o) {
		// 1.이름
		// 2.나이
		// 이름이 같다면.. 나이를 비교한다.
		if(this.name.equals(o.name)) {
			return this.age - o.age;
		}
		
		return this.name.compareTo(o.name);
		
	}
}
```

```java
package test07_comparable;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class SortTest {
    public static void main(String[] args) {
        List<Person> persons = new ArrayList<>();
     
        persons.add(new Person("BBB", 22));
        persons.add(new Person("BBB", 23));
        persons.add(new Person("AAA", 23));
        persons.add(new Person("AAA", 11));
        
        System.out.println(persons);
        
        // 대소 비교 기준이 없으므로 정렬할 수 없음
        // 1. 해당 클래스에 Comparable 인터페이스 구현
        // 2. Comparator 만들기 : 다른 클래스를 만들어서 => Comparator 객체를 활용.
        
         // Collections.sort(persons); // Person Comparable 인터페이스를 구현하고 있지 않으므로
         // Collections.sort 사용 불가.
        
        Collections.sort(persons);
     
        System.out.println(persons);
    }
}
```

위 코드의 출력 결과는 다음과 같다.

```
[Person [name=BBB, age=22], Person [name=BBB, age=23], Person [name=AAA, age=23], Person [name=AAA, age=11]]
[Person [name=AAA, age=11], Person [name=AAA, age=23], Person [name=BBB, age=22], Person [name=BBB, age=23]]
```

출력 결과를 보면, 이름 순으로 정렬이 된 것을 볼 수 있다. 그리고 이름이 같으면 나이 순으로 정렬이 되어 있다.

위 Person 클래스에서 주석 처리된 compareTo() 메서드는 나이 순으로 정렬이 되고, 나이가 같다면 이름 순으로 정렬을 시키는 코드이다.

<br>

이번에는 Comparator 객체를 활용해 정렬하는 것을 알아보겠다.

Comparator가 있다면, Comparable을 구현하지 않더라도 Comparator을 사용해서 정렬할 수 있다.

예시 코드는 다음과 같다.

우선 Person 클래스를 보면 다음과 같다.

```java
package test08_comparator;

// Comparable 인터페이스를 구현하고 있지 않아서
// 기본적으로 정렬이 불가능..
// Comparator가 있다면, Comparable을 구현하지 않더라도 Comparator를 사용해서 정렬할 수 있음.
public class Person {
    String name;
    int age;
    
    public Person(String name, int age) {
        super();
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person [name=" + name + ", age=" + age + "]";
    }   
}
```

Comparable 인터페이스를 구현하지 않고 있다. 기본적으로 정렬이 불가능한 상태이다.

그런데, Comparator가 있다면, Comparable 인터페이스를 구현하지 않더라도 정렬을 수행할 수 있다.

<br>

```java
package test08_comparator;

import java.util.Comparator;

public class PersonComparator implements Comparator<Person>{

	// this, o를 비교했다면
	// o1, o2를 비교
	
	
    @Override
    public int compare(Person o1, Person o2) {
        // 1. 이름순
        // 2. 나이순
        if (o1.name.equals(o2.name))
            return o1.age - o2.age; 
        
        return o1.name.compareTo(o2.name);
    }

}
```

위와 같이 하면 된다. Comparator은 기본적으로 클래스이고, Comparator을 implements 하면 된다.

또, 나이순으로 정렬하고 그 다음에 이름순으로 정렬을 수행하는 코드를 짠다면 다음과 같다.

```java
package test08_comparator;

import java.util.Comparator;

public class PersonComparator2 implements Comparator<Person>{

    @Override
    public int compare(Person o1, Person o2) {
        // 1. 나이순
        // 2. 이름순
        if(o1.age == o2.age) {
            return o1.name.compareTo(o2.name); 
        }
        return o1.age - o2.age; 
    }

}
```

Comparator은 제네릭(Generic)으로 되어 있다. 어떤 타입으로 정렬을 할 것인지 정하는 것이다. Person과 Person을 비교할 것이기 때문에, 위와 같이 Person 타입을 제네릭 타입으로 넣었다.

그리고 'Add implemented methods'를 클릭하여 compare()를 오버라이드 한다. int 값을 반환하는 것이다. 이번에는 comparator에게 두 가지 객체를 준다. 그러면 이게 두 개를 비교해서, 무엇이 더 큰지에 따라서 정수값을 반환하는 것이다.

<br>

다시 SortTest클래스를 보겠다.

```java
package test08_comparator;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class SortTest {
    public static void main(String[] args) {
        List<Person> persons = new ArrayList<>();

     
        persons.add(new Person("BBB", 22));
        persons.add(new Person("BBB", 23));
        persons.add(new Person("AAA", 23));
        persons.add(new Person("AAA", 11));
        
//        System.out.println(persons);
        
        // 대소 비교 기준이 없으므로 정렬할 수 없음
        // 1. 해당 클래스에 Comparable 인터페이스 구현
        // 2. Comparator 만들기 (Comparator 인터페이스 구현)
        
        
        Collections.sort(persons, new PersonComparator());	// 이름순 그리고 나이순 
        System.out.println(persons);
        
        Collections.sort(persons, new PersonComparator2());	// 나이순 그리고 이름순 
        System.out.println(persons);
    }
}
```

위 코드의 실행 결과는 다음과 같다.

```
[Person [name=AAA, age=11], Person [name=AAA, age=23], Person [name=BBB, age=22], Person [name=BBB, age=23]]
[Person [name=AAA, age=11], Person [name=BBB, age=22], Person [name=AAA, age=23], Person [name=BBB, age=23]]
```

만약 위 SortTest 코드에서 이전과 같이 ```Collections.sort(persons);```를 입력하면 안 된다. 이제는 Comparator을 넣어줘야 한다. PersonComparator을 만들었으니 ```Collections.sort(persons, new PersonComparator());```의 방식으로 사용해야 하는 것이다.

<br>

이번에는 SortTest2 클래스를 보겠다.

```java
package test08_comparator;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class SortTest2 {
    public static void main(String[] args) {
        List<Person> persons = new ArrayList<>();

        persons.add(new Person("BBB", 22));
        persons.add(new Person("BBB", 23));
        persons.add(new Person("AAA", 23));
        persons.add(new Person("AAA", 11));
        
        System.out.println(persons);
        
        // Comparator 클래스를 만들어도 되지만..
        // 람다 표현식
        // - 이름이 없는 함수
        // - 파라미터로 전달하기 위한 함수
        // - 주로 길이가 짧을 때
        
        // 문법
        // (매개변수..) -> {함수 본문내용}
        // 매개변수가 없을 때는? 빈괄호
        // 매개변수가 1개일 때는? 괄호생략가능
        //본문내용이 1문장만 있을 때는? 중괄호 생략 가능.
        // 매개변수의 타입을 생략할 수 있다.
        
        Collections.sort(persons,  (Person o1, Person o2) -> {
        	if(o1.name.equals(o2.name)) {
        		return o1.age - o2.age;
        	}
        	return o1.name.compareTo(o2.name);
        });
        
        System.out.println(persons);
    }
}
```

위 코드의 출력 결과는 다음과 같다.

```
[Person [name=BBB, age=22], Person [name=BBB, age=23], Person [name=AAA, age=23], Person [name=AAA, age=11]]
[Person [name=AAA, age=11], Person [name=AAA, age=23], Person [name=BBB, age=22], Person [name=BBB, age=23]]
```

Comparator을 사용할 때 익명 클래스를 사용할 수 있다.

1회성 객체 사용 시, ananymous inner class를 사용하는 것이다. 한 개만 만들어서 그 자리에서 바로 new 키워드로 사용하고 없어진다. Comparator은 인터페이스이다. 인터페이스는 그 자체로 객체 생성이 안 된다. 객체 생성을 하려면 추상메서드가 구현되어야 한다.

중괄호를 열고 이 안에서 바로 정의해버리는 것이다. 부족한 부분을 채우는 데에는 2가지 방법이 있다. 익명 클래스를 이용하는 방법과, 람다 표현식을 사용하는 방법이다.

위 SortTest2 클래스는 람다 표현식을 쓰고 있다. 람다 표현식이란 한 번 쓰고 사용하지 않는 것이다. 이름이 없는 함수이고, 파라미터로 전달하기 위한 함수라고 할 수 있다. 함수 구현의 길이가 짧을 때 임시로 사용한다.

매개변수가 1개일 때에는 괄호 생략이 가능하고, 본문내용이 1 문장만 있을 때에는 중괄호를 생략할 수 있으며, 매개변수의 타입을 생략할 수 있다.