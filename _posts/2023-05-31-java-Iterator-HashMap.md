---
layout: post
title: Iterator\<E\>, HashMap\<K, V\>
description: Java의 정석 기초편 ch. 12 지네릭스, 열거형, 애너테이션
categories: Java
date: 2023-05-31 16:50:00 +09:00
---
다음은 Iterator의 실제 소스인데, 컬렉션 클래스뿐만 아니라 Iterator에도 제네릭스가 적용되어 있는 것을 알 수 있다. 제네릭스가 도입되면서 기존의 소스에 Object가 들어간 클래스는 전부 이런 식으로 바뀌었다고 보면 된다.

(제네릭스가 이해가 잘 안 될 때는 타입변수를 Object로 바꿔서 생각해 보면 된다.)

```java
public interface Interator<E> {
  boolean hasNext();
  E next();
  void remove();
}
```

아래의 예제는 Iterator에 제네릭스를 적용한 예이다.

```java
import java.util.*;

class Ex12_2 {
	public static void main(String[] args) {
		ArrayList<Student> list = new ArrayList<Student>();
		list.add(new Student("자바왕", 1, 1));
		list.add(new Student("자바짱", 1, 2));
		list.add(new Student("홍길동", 2, 1));

		Iterator<Student> it = list.iterator();
		while (it.hasNext()) {
		//      Student s = (Student)it.next(); // 지네릭스를 사용하지 않으면 형변환 필요 
			Student s = it.next();
			System.out.println(s.name);
		}
	} // main
}

class Student {
	String name = "";
	int ban;
	int no;

	Student(String name, int ban, int no) {
		this.name = name;
		this.ban = ban;
		this.no = no;
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
자바왕
자바짱
홍길동
```


## HashMap\<K, V\>

HashMap처럼 데이터를 키(key)와 값(value)의 형태로 저장하는 컬렉션 클래스는 지정해줘야 할 타입이 두 개이다. 그래서 '\<K, V\>'와 같이 두 개의 타입을 콤마 ','로 구분해서 적어줘야 한다. 여기서 'K'와 'V'는 각각 'Key'의 'Value'의 첫 글자에서 따온 것일 뿐, 'T'나 'E'와 마찬가지로 임의의 참조형 타입(reference type)을 의미한다.

```java
public class HashMap<K, V> extends AbstractMap<K, V> {
    ...
  public V get(Object key) { /* 내용 생략 */ }
  public V put(K key, V value) { /* 내용 생략 */ }
  public V remove(Object key) { /* 내용 생략 */ }
    ...
}
```

만일 키의 타입이 String이고 저장할 값의 타입이 Student인 HashMap을 생성하려면 다음과 같이 한다.

```java
HashMap<String, Student> map = new HashMap<String, Student>();    // 생성
map.put("자바왕", new Student("자바왕", 1, 1, 100, 100, 100));       // 데이터 저장
```

위와 같이 HashMap을 생성하였다면, HashMap의 실제 소스는 'K' 대신 String이, 'V' 대신 Student가 사용되어 아래와 같이 바뀌는 셈이 된다.

```java
public class HashMap extends AbstractMap {
    ...
  public Student get(Object key) { /* 내용 생략 */ }
  public Student put(String key, Student value) { /* 내용 생략 */ }
  public Student remove(Object key) { /* 내용 생략 */ }
    ...
}
```

그래서 HashMap에서 값을 꺼내오는 get(Object key)를 사용할 때, 그리고 저장된 키와 값들을 꺼내오는 keySet()과 values()를 사용할 때 형변환을 하지 않아도 된다.

```java
// Student s1 = (Student)map.get("1-1");

Student s1 = map.get("1-1");
```
