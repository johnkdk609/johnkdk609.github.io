---
layout: post
title: Iterator, ListIterator, Enumeration
description: Java의 정석 기초편 ch. 11 컬렉션 프레임웍
categories: Java
date: 2023-05-19 15:03:00 +09:00
---
Iterator, ListIterator, Enumeration은 모두 컬렉션에 저장된 요소를 접근하는 데 사용되는 인터페이스이다. Enumeration은 Iterator의 구버전이며, ListIterator은 Iterator의 기능을 향상시킨 것이다.

> **Iterator** - 컬렉션에 저장된 요소를 접근하는 데 사용되는 인터페이스\
> **ListIterator** - Iterator에 양방향 조회 기능 추가(List를 구현한 경우만 사용 가능)\
> **Enumeration** - Iterator의 구버전

컬렉션 프레임워크에서는 컬렉션에 저장된 요소들을 읽어오는 방법을 표준화하였다. 컬렉션에 저장된 각 요소에 접근하는 기능을 가진 Iterator인터페이스를 정의하고, Collection인터페이스에는 'Iterator를 구현한 클래스의 인스턴스'를 반환하는 iterator()를 정의하고 있다. 

```java
public interface Iterator {
  boolean hasNext();
  Object next();
  void remove()
}
```

```java
public interface Collection {
    ...
  public Iterator iterator();
    ...
}
```

iterator()는 Collection인터페이스에 정의된 메서드이므로 Collection인터페이스의 자손인 List와 Set에도 포함되어 있다. 그래서 List나 Set인터페이스를 구현하는 컬렉션은 iterator()가 각 컬렉션의 특징에 알맞게 작성되어 있다. 컬렉션 클래스에 대한 iterator()를 호출하여 Iterator를 얻은 다음 반복문, 주로 while문을 사용해서 컬렉션 클래스의 요소들을 읽어올 수 있다. 

```java
List list = new ArrayList();      // 다른 컬렉션으로 변경할 때는 이 부분만 고치면 된다.
Iterator it = list.iterator();

while (it.hasNext()) {            // boolean hasNext() 읽어올 요소가 있는지 확인
  System.out.println(it.next());  // Object next() 다음 요소를 읽어옴
}
```

ArrayList 대신 List인터페이스를 구현한 다른 컬렉션 클래스에 대해서도 이와 동일한 코드를 사용할 수 있다. 첫 줄에서 ArrayList 대신 List인터페이스를 구현한 다른 컬렉션 클래스의 객체를 생성하도록 변경하기만 하면 된다.

Iterator를 이용해서 컬렉션의 요소를 읽어오는 방법을 표준화했기 때문에 이처럼 코드의 재사용성을 높이는 것이 가능한 것이다. 이처럼 공통 인터페이스를 정의해서 표준을 정의하고 구현하여 표준을 따르도록 함으로써 코드의 일관성을 유지하여 재사용성을 극대화하는 것이 객체지향 프로그래밍의 중요한 목적 중의 하나이다. 


## Iterator, ListIterator, Enumeration 예제 코드

```java
import java.util.*;

class Ex11_5 {
	public static void main(String[] args) {
		ArrayList list = new ArrayList();
		list.add("1");
		list.add("2");
		list.add("3");
		list.add("4");
		list.add("5");

		Iterator it = list.iterator();

		while(it.hasNext()) {
			Object obj = it.next();
			System.out.println(obj);
		}
	} // main
}
```

위 코드의 출력 결과는 다음과 같다.

```
1
2
3
4
5
```

List클래스들은 저장 순서를 유지하기 때문에 Iterator를 이용해서 읽어온 결과 역시 저장 순서와 동일하지만, Set클래스들은 각 요소 간의 순서가 유지되지 않기 때문에 Iterator를 이용해서 저장된 요소들을 읽어와도 처음에 저장된 순서와 같지 않다. 

List클래스들은 Iterator 대신 아래의 <보기 2>와 같이 for문과 get()으로도 모든 요소들을 출력할 수 있다.

```java
// <보기 1>

Iterator it = list.iterator();

while (it.hasNext()) {
  Object obj = it.next();
  System.out.println(obj);
}
```

```java
// <보기 2>

for (int i = 0; i < list.size(); i++) {
  Object obj = list.get(i);
  System.out.println(obj);
}
```


## Map과 Iterator

Map인터페이스를 구현한 컬렉션 클래스는 키(key)와 값(value)을 쌍(pair)으로 저장하고 있기 때문에 iterator()를 직접 호출할 수 없고, 그 대신 keySet()이나 entrySet()과 같은 메서드를 통해서 키와 값을 각각 따로 Set의 형태로 얻어온 후에 다시 iterator()를 호출해야 Iterator를 얻을 수 있다.

```java
Map map = new HashMap();
    ...
Iterator it = map.entrySet().iterator();
```

Iterator it = map.entrySet().iterator(); 는 아래의 두 문장을 하나로 합친 것이라고 이해하면 된다.

```java
Set eSet = map.entrySet();
Iterator it = eSet.iterator();
```

이 문장들의 실행 순서를 그림으로 그려보면 다음과 같다.

<img width="500" alt="[자바의 정석 - 기초편] ch11-22~24 Iterator, Enumeration, Map과 Iterator" title="[자바의 정석 - 기초편] ch11-22~24 Iterator, Enumeration, Map과 Iterator" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ccd4ca3a-d416-447e-842b-8314a0b54cd6">

① map.entrySet()의 실행 결과가 Set이므로
```java
Iterator it = map.entrySet().itertor();
→ Itertor it = Set인스턴스.iterator();
```

② map.entrySet()를 통해 얻은 Set인스턴스의 iterator()를 호출해서 Iterator인스턴스를 얻는다.
```java
Iterator it = Set인스턴스.iterator();
→ Iterator it = Iterator인스턴스;
```

③ 마지막으로 Iterator인스턴스의 참조가 it에 저장된다.
