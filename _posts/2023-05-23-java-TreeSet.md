---
layout: post
title: TreeSet
description: Java의 정석 기초편 ch. 11 컬렉션 프레임웍
categories: Java
date: 2023-05-23 16:32:00 +09:00
---
TreeSet은 이진 탐색 트리(binary search tree)라는 자료구조의 형태로 데이터를 저장하는 컬렉션 클래스이다. 이진 탐색 트리는 정렬, 검색, 범위검색(range search)에 높은 성능을 보이는 자료구조이며 TreeSet은 이진 탐색 트리의 성능을 향상시킨 '레드-블랙 트리(Red-Black tree)'로 구현되어 있다. 

그리고 **Set인터페이스를 구현했으므로 중복된 데이터의 저장을 허용하지 않으며, 정렬된 위치에 저장하므로 저장 순서를 유지하지도 않는다.**

이진 트리(binary tree)는 링크드 리스트처럼 여러 개의 노드(node)가 서로 연결된 구조로, 각 노드에 최대 2개의 노드를 연결할 수 있으며 '루트(root)'라고 불리는 하나의 노드에서부터 시작해서 계속 확장해 나갈 수 있다. 이진 탐색 트리는 이진 트리의 한 종류이다. 

위 아래로 연결된 두 노드를 '부모-자식관계'에 있다고 하며 위의 노드를 부모 노드, 아래의 노드를 자식 노드라 한다. 부모-자식관계는 상대적인 것이며 하나의 부모 노드는 최대 두 개의 자식 노드와 연결될 수 있다.

아래의 그림에서 A는 B와 C의 부모 노드이고, B와 C는 A의 자식 노드이다.

(트리(tree)는 각 노드 간의 연결된 모양이 나무와 같다고 해서 붙여진 이름이다.)

<img width="400" alt="[자바의 정석 - 기초편] ch11-39~41 TreeSet(1)" title="이진 트리(binary tree)의 예" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c3658a50-27ac-44e1-afe5-1dadea1d41d1">

이진 트리의 노드를 코드로 표현하면 다음과 같다.

```java
class TreeNode {
  TreeNode left;    // 왼쪽 자식 노드
  Object element;   // 객체를 저장하기 위한 참조변수
  TreeNode right;   // 오른쪽 자식 노드
}
```

데이터를 저장하기 위한 Object타입의 참조변수 하나와 두 개의 노드를 참조하기 위한 두 개의 참조변수를 선언했다.


## 이진 탐색 트리(binary search tree)

이진 탐색 트리(binary search tree)는 부모 노드의 왼쪽에는 부모 노드의 값보다 작은 값의 자식 노드를, 오른쪽에는 큰 값의 자식 노드를 저장하는 이진 트리이다.

'왼쪽 노드 → 부모 노드 → 오른쪽 노드' 순으로 읽어오면 오름차순으로 정렬된 순서를 얻을 수 있다. TreeSet은 이처럼 정렬된 상태를 유지하기 때문에 단일 값 검색과 범위검색(range search), 예를 들면 3과 7 사이의 범위에 있는 값을 검색하는 것이 매우 빠르다. 

저장된 값의 개수에 비례해서 검색 시간이 증가하기는 하지만 값의 개수가 10배 증가해도 특정 값을 찾는 데에 필요한 비교횟수가 3~4번만 증가할 정도로 검색 효율이 뛰어난 자료구조이다.

트리는 데이터를 순차적으로 저장하는 것이 아니라 저장 위치를 찾아서 저장해야 하고, 삭제하는 경우 트리의 일부를 재구성해야 하므로 링크드 리스트보다 데이터의 추가/삭제시간은 더 걸린다. 대신 배열이나 링크드 리스트에 비해 검색과 정렬 기능이 더 뛰어나다.

> **이진 탐색 트리(binary search tree)는**
> - 모든 노드는 최대 두 개의 자식 노드를 가질 수 있다.
> - 왼쪽 자식 노드의 값은 부모 노드의 값보다 작고, 오른쪽 자식 노드의 값은 부모 노드의 값보다 커야 한다.
> - 노드의 추가 삭제에 시간이 걸린다. (반복 비교로 자리를 찾아 저장)
> - 검색(범위검색)과 정렬에 유리하다.
> - 중복된 값을 저장하지 못한다.


## 이진 탐색 트리의 저장 과정

예를 들어 이진 탐색 트리에 7, 4, 9, 1, 5의 순서로 값을 저장한다고 가정하면 다음과 같은 순서로 진행된다.

<img width="700" alt="[자바의 정석 - 기초편] ch11-39~41 TreeSet(1)" title="[자바의 정석 - 기초편] ch11-39~41 TreeSet(1)" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4c1a4db3-f33d-4c89-90d7-410e64ea80a1">

첫 번째로 저장되는 값은 루트(root)가 되고, 두 번째 값은 트리의 루트부터 시작해서 값의 크기를 비교하면서 트리를 따라 내려간다. 작은 값은 왼쪽에 큰 값은 오른쪽에 저장한다. 이렇게 트리를 구성하면, 왼쪽 마지막 레벨이 제일 작은 값이 되고 오른쪽 마지막 레벨의 값이 제일 큰 값이 된다. 

앞서 살펴본 것처럼, 컴퓨터는 알아서 값을 비교하지 못한다. TreeSet에 저장되는 객체가 Comparable을 구현하든가, 아니면 Comparator을 제공해서 두 객체를 비교할 방법을 알려줘야 한다. 그렇지 않으면, TreeSet에 객체를 저장할 때 예외가 발생한다. 


## TreeSet 예제 1

```java
import java.util.*;

class Ex11_13 {
	public static void main(String[] args) {
		Set set = new TreeSet();
		
		for (int i = 0; set.size() < 6 ; i++) {
			int num = (int)(Math.random()*45) + 1;
			set.add(num);  // set.add(new Integer(num));
		}

		System.out.println(set);
	}
}
```

위 코드의 출력 결과 예시는 다음과 같다.

```
[2, 4, 11, 35, 37, 45]
```

이전의 예제 11-10을 TreeSet을 사용해서 바꾸었다. 이전 예제와는 달리 정렬하는 코드가 빠져 있는데, TreeSet은 저장할 때 이미 정렬하기 때문에 읽어올 때 따로 정렬할 필요가 없기 때문이다.


## TreeSet 예제 2

```java
import java.util.*;

class Ex11_14 {
	public static void main(String[] args) {
		TreeSet set = new TreeSet();

		String from = "b";
		String to = "d";

		set.add("abc");      set.add("alien");    set.add("bat");
		set.add("car");      set.add("Car");      set.add("disc");
		set.add("dance");    set.add("dZZZZ");    set.add("dzzzz");
		set.add("elephant"); set.add("elevator"); set.add("fan");
		set.add("flower");

		System.out.println(set);
		System.out.println("range search : from " + from  +" to "+ to);
		System.out.println("result1 : " + set.subSet(from, to));
		System.out.println("result2 : " + set.subSet(from, to + "zzz"));
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
[Car, abc, alien, bat, car, dZZZZ, dance, disc, dzzzz, elephant, elevator, fan, flower]
range search : from b to d
result1 : [bat, car]
result2 : [bat, car, dZZZZ, dance, disc]
```

subSet()을 이용해서 범위검색(range search)할 때 시작 범위는 포함되지만 끝 범위는 포함되지 않으므로 result1에는 c로 시작하는 단어까지만 검색 결과에 포함되어 있다.

만일 끝 범위인 d로 시작하는 단어까지 포함시키고자 한다면, 아래와 같이 끝 범위에 'zzz'와 같은 문자열을 붙이면 된다.

```java
System.out.println("result2 : " + set.subSet(from, to + "zzz"));
```

d로 시작하는 단어 중에서 'dzzz' 다음에 오는 단어는 없을 것이기 때문에 d로 시작하는 모든 단어들이 포함될 것이다.

결과를 보면 'abc'보다 'Car'가 앞에 있고 'dZZZZ'가 'dance'보다 앞에 정렬되어 있는 것을 알 수 있다. 대문자가 소문자보다 우선하기 때문에 대소문자가 섞여 있는 경우 의도한 것과는 다른 범위검색결과를 얻을 수 있다.

그래서 가능하면 대문자 또는 소문자로 통일해서 저장하는 것이 좋다.


## TreeSet 예제 3

```java
import java.util.*;

class Ex11_15 {
	public static void main(String[] args) {
		TreeSet set = new TreeSet();
		int[] score = {80, 95, 50, 35, 45, 65, 10, 100};

		for(int i=0; i < score.length; i++)
			set.add(new Integer(score[i]));

		System.out.println("50보다 작은 값 :" + set.headSet(new Integer(50)));
		System.out.println("50보다 큰 값 :"  + set.tailSet(new Integer(50)));
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
50보다 작은 값 :[10, 35, 45]
50보다 큰 값 :[50, 65, 80, 95, 100]
```

headSet메서드와 tailSet메서드를 이용하면, TreeSet에 저장된 객체 중 지정된 기준 값보다 큰 값의 객체들과 작은 값의 객체들을 얻을 수 있다.

예제에 사용된 값들로 이진 검색 트리를 구성해보면 다음 그림과 같다.

<img width="750" alt="[자바의 정석 - 기초편] ch11-42~45 TreeSet(2)" title="예제 11-15에 사용된 데이터로 구성한 이진 검색 트리" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/34f7b9f8-e422-4281-b81d-fcb121058602">

위의 그림을 보면 50이 저장된 노드의 왼쪽 노드와 그 아래 연결된 모든 노드의 값은 50보다 작고, 나머지 다른 노드의 값들은 50보다 같거나 크다는 것을 알 수 있다.
