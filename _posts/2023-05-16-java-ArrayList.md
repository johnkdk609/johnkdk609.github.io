---
layout: post
title: ArrayList
description: Java의 정석 기초편 ch. 11 컬렉션 프레임웍
categories: Java
date: 2023-05-16 14:55:00 +09:00
---
ArrayList는 컬렉션 프레임워크에서 가장 많이 사용되는 컬렉션 클래스일 것이다. **ArrayList는 List인터페이스를 구현하기 때문에 데이터의 저장 순서가 유지되고 중복을 허용한다는 특징을 갖는다.**

ArrayList는 기존의 Vector를 개선한 것으로 Vector와 구현 원리가 기능적인 측면에서 동일하다고 할 수 있다. Vector는 기존에 작성된 소스와의 호환성을 위해서 계속 남겨 두고 있을 뿐이기 때문에 가능하면 Vector보다 ArrayList를 사용하는 것이 좋다. 

ArrayList는 **Object배열을 이용해서 데이터를 순차적으로 저장한다.** 예를 들면, 첫 번째로 저장한 객체는 Object배열의 0번째 위치에 저장하고 그 다음에 저장하는 객체는 1번째 위치에 저장된다. 이런 식으로 계속 배열에 순서대로 저장되며, **배열에 더 이상 저장할 공간이 없으면 보다 큰 새로운 배열을 생성해서 기존의 배열에 저장된 내용을 새로운 배열로 복사한 다음에 저장**된다. 

```java
public class ArrayList extends AbstractList implements List, RandomAccess, Cloneable, java.io.Serializable {
    ...
  transient Object[] elementData;   // Object배열
    ...
}
```

(transient는 직렬화(serialization)와 관련된 제어자이다.)

위의 코드는 ArrayList의 소스코드 일부인데 ArrayList는 elementData라는 이름의 Object배열을 멤버변수로 선언하고 있다는 것을 알 수 있다. 선언된 배열의 타입이 모든 객체의 최고조상인 Object이기 때문에 모든 종류의 객체를 담을 수 있다.


## ArrayList 예제 코드

```java
import java.util.*;

class Ex11_1 {
	public static void main(String[] args) {
		ArrayList list1 = new ArrayList(10);
		list1.add(new Integer(5));
		list1.add(new Integer(4));
		list1.add(new Integer(2));
		list1.add(new Integer(0));
		list1.add(new Integer(1));
		list1.add(new Integer(3));

		ArrayList list2 = new ArrayList(list1.subList(1,4)); 
		print(list1, list2);

		Collections.sort(list1);	// list1과 list2를 정렬한다.
		Collections.sort(list2);	// Collections.sort(List l)
		print(list1, list2);

		System.out.println("list1.containsAll(list2):" + list1.containsAll(list2));

		list2.add("B");
		list2.add("C");
		list2.add(3, "A");
		print(list1, list2);

		list2.set(3, "AA");
		print(list1, list2);

		// list1에서 list2와 겹치는 부분만 남기고 나머지는 삭제한다. 
		System.out.println("list1.retainAll(list2):" + list1.retainAll(list2));

		print(list1, list2);

		//  list2에서 list1에 포함된 객체들을 삭제한다. 
		for(int i= list2.size()-1; i >= 0; i--) {
			if(list1.contains(list2.get(i)))
				list2.remove(i);	// 인덱스가 i인 곳에 저장된 요소를 삭제 
		}
		print(list1, list2);
	} // main의 끝 

	static void print(ArrayList list1, ArrayList list2) {
		System.out.println("list1:"+list1);
		System.out.println("list2:"+list2);
		System.out.println();		
	}
} // class
```

위 코드의 출력 결과는 다음과 같다.

```
list1:[5, 4, 2, 0, 1, 3]
list2:[4, 2, 0]

list1:[0, 1, 2, 3, 4, 5]
list2:[0, 2, 4]

list1.containsAll(list2):true
list1:[0, 1, 2, 3, 4, 5]
list2:[0, 2, 4, A, B, C]

list1:[0, 1, 2, 3, 4, 5]
list2:[0, 2, 4, AA, B, C]

list1.retainAll(list2):true
list1:[0, 2, 4]
list2:[0, 2, 4, AA, B, C]

list1:[0, 2, 4]
list2:[AA, B, C]
```

위의 예제는 ArrayList의 기본적인 메서드를 이용해서 객체를 다루는 방법을 보여준다. ArrayList는 List인터페이스를 구현했기 때문에 저장된 순서를 유지한다는 것을 알 수 있다. 그리고 Collections클래스의 sort메서드를 이용해서 ArrayList에 저장된 객체들을 정렬하였다.

(Collection은 인터페이스이고, Collections은 클래스임에 주의해야 한다.)


## ArrayList의 추가와 삭제

ArrayList의 요소를 삭제하는 경우, 삭제할 객체의 바로 아래에 있는 데이터를 한 칸씩 위로 복사해서 삭제할 객체를 덮어쓰는 방식으로 처리한다. 만일 삭제할 객체가 마지막 데이터라면, 복사할 필요 없이 단순히 null로 변경해주기만 하면 된다.

아래의 그림은 ArrayList에 0~4의 값이 저장되어 있는 상태에서 세 번째 데이터를 삭제하기 위해 remove(2)를 호출했다고 가정하고, 그 수행 과정의 일부를 단계별로 나타낸 것이다.

<img width="900" alt="[자바의 정석 - 기초편] ch11-7~11 ArrayList" title="[자바의 정석 - 기초편] ch11-7~11 ArrayList" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b5917b9c-6972-4932-8514-7acbb472bb72">

**① 삭제할 데이터의 아래에 있는 데이터를 한 칸씩 복사해서 삭제할 데이터를 덮어쓴다.**\
**② 데이터가 모두 한 칸씩 위로 이동하였으므로 마지막 데이터는 null로 변경해야 한다.**\
**③ 데이터가 삭제되어 데이터의 개수(size)가 줄었으므로 size의 값을 1 감소시킨다.**

위 과정을 통해 배워야 할 것은 배열에 객체를 순차적으로 저장할 때와 객체를 마지막에 저장된 것부터 삭제하면 데이터를 옮기지 않아도 되기 때문에 작업시간이 짧지만, 배열의 중간에 위치한 객체를 추가하거나 삭제하는 경우 다른 데이터의 위치를 이동시켜 줘야 하기 때문에 다루는 데이터의 개수가 많을수록 작업시간이 오래 걸린다는 것이다. 

ArrayList에 새로운 요소를 추가할 때도 먼저 추가할 위치 이후의 요소들을 모두 한 칸씩 이동시킨 후에 저장해야 한다.
