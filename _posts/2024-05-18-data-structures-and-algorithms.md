---
layout: post
title: 자료구조 & 알고리즘 생각해볼 만한 것들
categories: Algorithm
date: 2024-05-18 10:16:00 +0900
---
어떤 문제에 대해 시간 복잡도(Time Complexity)를 개선할 수 있는지 여부를 체크하는 것은 상당히 중요한 부분이다.

## 정렬 알고리즘 비교

우선 다양한 정렬 알고리즘의 시간 복잡도와 특징들을 표로 정리해보겠다.

<table>
    <tr>
        <th style="text-align: center;">알고리즘</th>
        <th style="text-align: center;">평균 수행시간</th>
        <th style="text-align: center;">최악 수행시간</th>
        <th style="text-align: center;">알고리즘 기법</th>
        <th style="text-align: center;">비고</th>
    </tr>
    <tr>
        <td style="text-align: center;">버블 정렬</td>
        <td style="text-align: center;">O(n²)</td>
        <td style="text-align: center;">O(n²)</td>
        <td style="text-align: center;">비교와 교환</td>
        <td>코딩이 가장 손쉽다.</td>
    </tr>
    <tr>
        <td style="text-align: center;">선택 정렬</td>
        <td style="text-align: center;">O(n²)</td>
        <td style="text-align: center;">O(n²)</td>
        <td style="text-align: center;">비교와 교환</td>
        <td>교환의 회수가 버블, 삽입정렬보다 작다.</td>
    </tr>
    <tr>
        <td style="text-align: center;">카운팅 정렬</td>
        <td style="text-align: center;">O(n+k)</td>
        <td style="text-align: center;">O(n+k)</td>
        <td style="text-align: center;">비교환 방식</td>
        <td>n이 비교적 작을 때만 가능하다.</td>
    </tr>
    <tr>
        <td style="text-align: center;">삽입 정렬</td>
        <td style="text-align: center;">O(n²)</td>
        <td style="text-align: center;">O(n²)</td>
        <td style="text-align: center;">비교와 교환</td>
        <td>n의 개수가 작을 때 효과적이다.</td>
    </tr>
    <tr>
        <td style="text-align: center;">병합 정렬</td>
        <td style="text-align: center;">O(n log n)</td>
        <td style="text-align: center;">O(n log n)</td>
        <td style="text-align: center;">분할 정복</td>
        <td>연결리스트의 경우 가장 효율적인 방식</td>
    </tr>
    <tr>
        <td style="text-align: center;">퀵 정렬</td>
        <td style="text-align: center;">O(n log n)</td>
        <td style="text-align: center;">O(n²)</td>
        <td style="text-align: center;">분할 정복</td>
        <td>최악의 경우 O(n²)이지만, 평균적으로는 가장 빠르다.</td>
    </tr>
</table>

<br>

## 피보나치 Memoization 기법

먼저 피보나치 수를 구하는 재귀함수 코드는 다음과 같다.

```java
public static int fibo(int n) {
    if (n == 1 || x == 2) {
        return 1;
    } else {
        return fibo(n - 1) + fibo(n - 2);
    }
}
```

그런데 위와 같이 피보나치 수를 구할 때 재귀 함수로 구현한 알고리즘은 문제점이 있다.

"엄청난 중복 호출이 존재한다"는 것이다.

이를 해결하기 위해 <b>Memoization 기법을 사용</b>할 것이다. 위의 피보나치 수를 구하는 알고리즘에서 fibo(n)의 값을 계산하자마자 저장하면(memoize), 실행시간을 Θ(n)으로 줄일 수 있다.

Memoization 방법을 적용한 알고리즘은 다음과 같다.

```java
// Memoization array to store Fibonacci values
private static int[] memo;  // 사용하려면 초기화 해야 한다.

// Recursive method to find the nth Fibonacci number with memoization
public static int fibo(int n) {
    if (n <= 0) {
        return 0; // Handle edge case for non-positive integers
    }
    if (memo[n] != 0) {
        return memo[n]; // Return already computed value
    }
    if (n == 1 || n == 2) {
        memo[n] = 1; // Base cases
    } else {
        memo[n] = fibo(n - 1) + fibo(n - 2); // Recursive case with memoization
    }
    return memo[n];
}
```

<br>

## 연결 리스트 (Linked List)

링크드리스트는 자료의 논리적인 순서와 메모리 상의 물리적인 순서가 일치하지 않고, 개별적으로 위치하고 있는 원소의 주소를 연결하여 하나의 전체적인 자료구조를 이룬다.

링크를 통해 원소에 접근하므로, 순차 리스트에서처럼 물리적인 순서를 맞추기 위한 작업이 필요하지 않다.

자료구조의 크기를 동적으로 조정할 수 있어, 메모리의 효율적인 사용이 가능하다.

예시 코드는 다음과 같다.

```java
import java.util.LinkedList;
import java.util.List;

public class LinkedList_Practice {
	public static void main(String[] args) {
		List<String> fruit = new LinkedList<>();
		fruit.add("apple");
		fruit.add("banana");
		fruit.add("orange");
		System.out.println(fruit);
		
		fruit.add(1, "grape");	// 원하는 위치에 삽입 
		System.out.println(fruit);
		System.out.println(fruit.size()); 	// 전체 개수 
		
		String secondFruit = fruit.get(1);	// 인덱스 값 
		System.out.println(secondFruit);
		
		fruit.remove("apple");	// 값으로 제거 
		System.out.println(fruit);
		
		fruit.remove(2);	// 인덱스로 제거 
		System.out.println(fruit);
		
		fruit.clear();
		System.out.println(fruit.size());
	}
}
```

위 코드의 출력 결과는 다음과 같다.

<img width="427" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/92a659e7-6019-4d18-b039-d035af2a286a">

<br>

## 힙 (Heap)

힙(Heap)은 완전 이진 트리에 있는 노드 중에서 키 값이 가장 큰 노드나 키 값이 가장 작은 노드를 찾기 위해서 만든 자료구조이다.

* 최대 힙(max heap)

    - 키 값이 가장 큰 노드를 찾기 위한 완전 이진 트리
    - 부모 노드의 키 값 &#62;= 자식 노드의 키 값
    - 루트 노드 : 키 값이 가장 큰 노드

* 최소 힙(min heap)

    - 키 값이 가장 작은 노드를 찾기 위한 완전 이진 트리
    - 부모 노드의 키 값 &#60;= 자식 노드의 키 값
    - 루트 노드 : 키 값이 가장 작은 노드

가령 최대 힙의 경우 삽입 연산을 할 때에는, 트리의 말단에 노드를 삽입했는데 부모 노드보다 값이 크다면 swap을 진행한다. 그 과정을 최상단 루트 노드까지 진행하는 것이다.

그리고 <b>힙에서는 루트 노드의 원소만을 삭제할 수 있다.</b> 루트 노드의 원소를 삭제하여 반환한다.

힙의 종류에 따라 최댓값 또는 최솟값을 구할 수 있다.

우선순위 큐를 구현하는 가장 효율적인 방법은 힙을 사용하는 것이다. 노드 하나의 추가 / 삭제 시간 복잡도가 O(log N)이고 최댓값 / 최솟값을 O(1)에 구할 수 있다. 그리고 완전 정렬보다 관리 비용이 적다.

그리고 배열을 통해 트리 형태를 쉽게 구현할 수 있다는 특징이 있다. 부모나 자식 노드를 O(1) 연산으로 쉽게 찾을 수 있는 것이다. n의 위치에 있는 노드의 자식은 2 * n 과 (2 * n) + 1 에 위치한다.

완전 이진 트리의 특성에 의해 추가 / 삭제의 위치는 자료의 시작과 끝 인덱스로 쉽게 판단할 수 있다.

<br>

## HashMap

자바의 HashMap은 파이썬의 딕셔너리와 비슷한 것이다. 즉, key-value로 데이터에 접근할 수 있는 강력한 툴인 것이다.

HashMap의 장점은 다음과 같다.

1. 빠른 검색 및 삽입
2. 유연한 크기 조정
3. 다양한 데이터 형식 저장 가능

특히 검색, 삽입, 삭제 연산이 빈번한 상황에서 뛰어난 성능을 발휘한다.

삽입은 평균적으로 O(1), 최악의 경우 O(N)
검색 및 삭제는 평균적으로 O(1), 최악의 경우 O(N)

HashMap은 중복 Key 값을 허용하지 않지만, 중복 Value 값은 허용한다.

값을 추가할 때 주의할 점이 있다. HashMap의 경우 저장공간보다 추가적으로 데이터가 들어오면 파이썬의 리스트처럼 공간을 늘리기는 하지만, 한 번에 약 2배로 늘린다. 그래서 이런 과정에서 과부하가 많이 발생한다.

따라서, 저장할 데이터의 개수를 미리 알고 있다면, 마치 배열의 길이를 미리 설정하듯 HashMap의 길이를 미리 설정할 수 있다.

HashMap은 다음과 같이 사용된다.

```java
import java.util.HashMap;

public class HashMap_Practice {

	public static void main(String[] args) {
		
		HashMap<Integer, String> map1 = new HashMap<>();
		HashMap<Integer, Integer> map2 = new HashMap<>(10); // 길이 미리 설정
		
		map1.put(1, "king");
		map1.put(2, "queen");
		map1.put(3, "prince");
		map1.put(4, "princess");
		System.out.println(map1);
		System.out.println(map1.get(3));
		map1.remove(3);
		System.out.println(map1);
		map1.put(3, "duke");
		System.out.println(map1);
		System.out.println(map1.containsKey(5));
		System.out.println(map1.containsKey(4));
		System.out.println(map1.size());
		map1.clear();
		System.out.println(map1);
		System.out.println(map1.size());
	}
}
```

위 코드의 출력 결과는 다음과 같다.

<img width="474" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/192409e2-5400-46c3-bf34-bb89488de7f7">

위 코드에서 쓰인 메서드들은 다음과 같다.

* ```put()``` : 생성한 HashMap에 요소를 넣는 것. key-value 쌍으로 넣어야 한다.
* ```get()``` : 괄호 안에 key 값을 넣어서 값을 찾는 것
* ```remove()``` : 괄호 안에 key 값을 넣어서 HashMap의 해당 요소를 지운다.
* ```containsKey()``` : 괄호 안에 key 값을 넣어서 HashMap에 해당 key가 있는지 없는지 boolean 형태로 반환한다.
* ```clear()``` : 해당 HashMap에 있는 요소들을 전부 지운다.
* ```size()``` : 해당 HashMap에 있는 요소의 개수는 몇 개인지 카운트해서 반환한다.

<br>

## HashSet

HashSet은 객체 자체를 데이터로 저장하기 때문에 중복을 허용하지 않는다. (HashMap이 HashSet 보다 빠르다.)

그리고 HashSet에서는 요소의 순서가 보장되지 않는다.

삽입은 평균적으로 O(1), 최악의 경우 O(N)
검색, 삭제는 평균적으로 O(1), 최악의 경우 O(N)

HashSet을 구현한 예시 코드는 다음과 같다.

```java
import java.util.HashSet;

public class HashSet_Practice {
	public static void main(String[] args) {
		
		HashSet<String> hashSet = new HashSet<>();
		hashSet.add("apple");
		hashSet.add("banana");
		hashSet.add("orange");
		hashSet.add("apple");
		
		System.out.println(hashSet.size());
		
		for (String fruit : hashSet) {
			System.out.println(fruit);
		}
		
		System.out.println(hashSet.contains("orange"));
		System.out.println(hashSet.contains("kiwi"));
		
		hashSet.remove("orange");
		System.out.println(hashSet.size());
		System.out.println(hashSet);
		hashSet.clear();
		System.out.println(hashSet);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

<img width="277" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/74036280-9ee9-4dce-8422-abd66e4d6fc3">

위 코드에서 사용된 메서드는 다음과 같다.

* ```add()``` : HashSet에 요소 추가
* ```size()``` : HashSet의 요소의 개수 반환
* ```contains()``` : 괄호에 값 넣어서 HashSet에 그 값이 존재하는지 검토. boolean 형 반환.
* ```remove()``` : 괄호에 값 넣어서 HashSet에 해당 값 삭제
* ```clear()``` : HashSet에 있는 요소들 전부 삭제

<br>

## ArrayList와 Collections.sort()

ArrayList는 Array와 List의 장점을 각각 갖고 있다.

배열의 특성인 index로 식별자 사용이 가능하고, 리스트의 특성대로 크기를 동적으로 사용할 수 있다. 즉, index를 사용할 수 있는 리스트인 것이다.

그런데 성능이 중요한 경우에는 고정 크기의 배열을 사용하는 것이 낫고, 유연성이나 코드의 편의성을 고려할 때에는 ArrayList를 사용하는 것이 좋다. 사실 성능 차이가 작은 경우가 많기 때문에 대부분의 상황에서는 ArrayList를 사용하는 것이 바람직하다.

그리고 Collections.sort()를 이용해 오름차순 정렬을 하거나, Collections.sort(arrayList, Collections.reversOrder())를 하여 내림차순 정렬을 할 수 있다.

예시 코드는 다음과 같다.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Collections_sort_Practice {

	public static void main(String[] args) {
		
		List<Integer> list1 = new ArrayList<>();
		list1.add(1);
		list1.add(100);
		list1.add(10);
		list1.add(4);
		list1.add(30);
		list1.remove(4);	// 인덱스 값을 넣어야 한다. 
		System.out.println(list1);
		Collections.sort(list1);	// Collections.sort() 를 활
		System.out.println(list1);
		Collections.sort(list1, Collections.reverseOrder());	// 역순 정렬 
		System.out.println(list1);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

<img width="217" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/36da2b05-e2ed-4534-b396-f789773636c3">

위 코드에서는 다형성을 적용하였다. List 인터페이스의 구현체인 ArrayList를 List와 같이 사용한 것이다.

<br>

## ArrayList를 Array로 변환하기

ArrayList에서 얻은 배열리스트를 일반 배열로 변환해야 할 수도 있다.

다음과 같이 수행한다.

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class ArrayList_to_Array {
	public static void main(String[] args) {
		
		List<Integer> arrayList = new ArrayList<>();
		arrayList.add(1);
		arrayList.add(2);
		arrayList.add(3);
		arrayList.add(100);
		
		Integer[] arr = new Integer[arrayList.size()];
		arr = arrayList.toArray(arr);
		
		System.out.println(Arrays.toString(arr));
		
	}
}
```

위 코드의 출력 결과는 다음과 같다.

<img width="195" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1c9ef77e-ddf1-4a1e-bcbf-d5db2f53622a">

<br>

## Queue

Queue는 FIFO(First In First Out) 자료구조이다.

사용 예제를 보면 다음과 같다.

```java
import java.util.LinkedList;
import java.util.Queue;

public class Queue_Practice {
	public static void main(String[] args) {
		Queue<Integer> queue1 = new LinkedList<>();
		queue1.offer(1);
		queue1.offer(2);
		queue1.offer(3);
		System.out.println(queue1.peek());
		System.out.println(queue1.poll());
		queue1.offer(4);
		queue1.offer(5);
		System.out.println(queue1);
		System.out.println(queue1.size());
		queue1.poll();
		queue1.poll();
		queue1.poll();
		queue1.poll();
		System.out.println(queue1);
		System.out.println(queue1.poll());
	}
}
```

위 코드의 출력 결과는 다음과 같다.

<img width="435" alt="image" src="https://github.com/johnkdk609/pjt-tutorin/assets/88493727/50b442ed-df7a-4c9a-8935-e89ad1d6635e">

위 코드에서 쓰인 메서드들은 다음과 같다.

* ```offer()``` : ```add()```와 마찬가지로 Queue에 추가하는 메서드
* ```peek()``` : Queue의 첫 번째로 들어간 값 참조 (꺼내는 것 아님)
* ```size()``` : Queue의 요소의 개수 반환
* ```poll()``` : Queue에서 맨 앞에 있는 것 하나 빼는 메서드 (반환함)

이때 Queue에서 offer() 과 add() 의 차이는 다음과 같다.

offer() 는 지정된 요소를 큐에 추가하고 성공 여부를 boolean 형태로 반환한다. 만약 큐가 공간 부족으로 요소를 추가하지 못하면 false를 반환한다. 일반적으로 offer()는 큐에 요소를 추가할 때 예외가 발생하지 않는다.

add() 는 지정된 요소를 큐에 추가하고 성공 여부를 boolean으로 반환한다. 만약 큐에 공간이 부족하여 요소를 추가하지 못하는 경우 'IllegalStateException'을 발생시킨다. 따라서 add() 는 요소를 추가할 때 큐의 용량이 부족할 경우 예외를 발생시킬 수 있다.

<br>

## Stack

스택은 FILO (First In Last Out) 자료구조이다.

예시 코드는 다음과 같다.

```java
import java.util.Stack;

public class Stack_Practice {
	public static void main(String[] args) {
		
		Stack<String> stack = new Stack<>();
		
		// 문자열 요소 추가
        stack.push("apple");
        stack.push("banana");
        stack.push("orange");

        // 스택의 상태 출력
        System.out.println("스택의 요소:");
        System.out.println(stack);

        // 요소 제거 및 출력
        System.out.println("스택에서 요소를 제거합니다:");
        while (!stack.isEmpty()) {
            String removedElement = stack.pop();
            System.out.println("제거된 요소: " + removedElement);
        }
	}
}
```

위 코드의 출력 결과는 다음과 같다.

<img width="311" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7781300a-3f5f-44e8-9d55-868694935795">

<br>

## PriorityQueue (우선순위 큐)

우선순위 큐(Priority Queue)는 배열을 이용하여 구현할 수도 있고, 리스트를 이용하여 구현할 수도 있다.

<u>우선순위 큐를 배열을 이용하여 구현하면, 삽입이나 삭제 연산이 일어날 때 원소의 재배치가 발생</u>한다. 이에 소요되는 시간이나 메모리 낭비가 큰 것이다.

반면, <u>우선순위 큐를 리스트를 이용하여 구현하면 삽입/삭제 연산 이후 원소 재배치가 필요 없기에</u> 메모리의 효율적인 사용이 가능하다.

내부 요소는 힙으로 구성되어 이진트리 구조로 이루어져 있다. 내부구조가 힙으로 구성되어 있기에 시간 복잡도는 O(N log N)이다. 우선순위를 중요시해야 하는 상황에서 주로 쓰인다.

우선순위 큐를 코드로 구현한 것은 다음과 같다.

```java
import java.util.PriorityQueue;

public class PriorityQueue_Practice {

	public static void main(String[] args) {
		
		PriorityQueue<Integer> pq = new PriorityQueue<>();
		
		// 앞에서 추가
        pq.offer(10);
        pq.offer(20);
        pq.offer(15);
        pq.offer(5);
        System.out.println("앞에서 추가 후 우선순위 큐의 요소: " + pq);

        // 뒤에서 추가
        pq.offer(25);
        pq.offer(30);
        pq.offer(40);
        System.out.println("뒤에서 추가 후 우선순위 큐의 요소: " + pq);

        // 앞에서 제거
        pq.poll();
        pq.poll();
        System.out.println("앞에서 제거 후 우선순위 큐의 요소: " + pq);

        // 뒤에서 제거
        pq.remove(40);
        pq.remove(30);
        System.out.println("뒤에서 제거 후 우선순위 큐의 요소: " + pq);
        
        System.out.println(pq.isEmpty());
        System.out.println(!pq.isEmpty());
	}
}
```

위 코드의 출력 결과는 다음과 같다.

<img width="630" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4cbf0d78-757e-4b8b-b924-899519d0f3bc">

<br>

## 이진 탐색 알고리즘 (Binary Search)

```java

public class Array2_02_BinarySearch {

	public static void main(String[] args) {
		int[] arr = {23, 44, 55, 77, 84, 91};
		int result = binarySearch(arr, 77);
		System.out.println(result);
	}
	
	public static int binarySearch(int[] arr, int key) {
		int left = 0;
		int right = arr.length - 1;
		
		while(left <= right) {
			int mid = (left + right) / 2;
			if (key == arr[mid]) {
				return mid;
			} else if (key < arr[mid]) {
				right = mid - 1;
			} else {
				left = mid + 1;
			}
		}
		
		return -1;
	}
}
```