---
layout: post
title: HashSet
description: Java의 정석 기초편 ch. 11 컬렉션 프레임웍
categories: Java
date: 2023-05-22 17:08:00 +09:00
---
HashSet은 Set인터페이스를 구현한 가장 대표적인 컬렉션이며, Set인터페이스의 특징대로 **HashSet은 중복된 요소를 저장하지 않는다.**

HashSet에 새로운 요소를 추가할 때는 add메서드나 addAll메서드를 사용하는데, 만일 HashSet에 이미 저장되어 있는 요소와 중복된 요소를 추가하고자 한다면 이 메서드들은 false를 반환함으로써 중복된 요소이기 때문에 추가에 실패했다는 것을 알린다.

이러한 HashSet의 특징을 이용하면, 컬렉션 내의 중복된 요소들은 쉽게 제거할 수 있다.

ArrayList와 같이 List인터페이스를 구현한 컬렉션과 달리 HashSet은 저장 순서를 유지하지 않으므로 저장 순서를 유지하고자 한다면 LinkedHashSet을 사용해야 한다.


## HashSet 예제 1

```java
import java.util.*;

class Ex11_9 {
	public static void main(String[] args) {
		Object[] objArr = {"1",new Integer(1),"2","2","3","3","4","4","4"};
		Set set = new HashSet();

		for(int i=0; i < objArr.length; i++) {
			set.add(objArr[i]);	// HashSet에 objArr의 요소들을 저장한다.
		}
                // HashSet에 저장된 요소들을 출력한다.
		System.out.println(set);	

		// HashSet에 저장된 요소들을 출력한다.(Iterator이용)
		Iterator it = set.iterator();
		
		while(it.hasNext()) {
			System.out.println(it.next());	
		}
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
[1, 1, 2, 3, 4]
1
1
2
3
4
```

결과에서 알 수 있듯이 중복된 값은 저장되지 않았다. add메서드는 객체를 추가할 때 HashSet에 이미 같은 객체가 있으면 중복으로 간주하고 저장하지 않는다. 그리고는 작업이 실패했다는 의미로 false를 반환한다.

'1'이 두 번 출력되었는데, 둘 다 '1'로 보이기 때문에 구별이 안 되지만, 사실 하나는 String인스턴스이고 다른 하나는 Integer인스턴스로 서로 다른 객체이므로 중복으로 간주하지 않는다.

Set을 구현한 컬렉션 클래스는 List를 구현한 컬렉션 클래스와 달리 순서를 유지하지 않기 때문에 저장한 순서와 다를 수 있다.

만일 중복을 제거하는 동시에 저장한 순서를 유지하고자 한다면 HashSet 대신 LinkedHashSet을 사용해야 한다.


## HashSet 예제 2

```java
import java.util.*;

class Ex11_10 {
	public static void main(String[] args) {
		Set set = new HashSet();
		
		for (int i = 0; set.size() < 6 ; i++) {
			int num = (int)(Math.random()*45) + 1;
			set.add(new Integer(num));
		}

		List list = new LinkedList(set); // LinkedList(Collection c)
		Collections.sort(list);          // Collections.sort(List list)
		System.out.println(list);
	}
}
```

위 코드의 출력 결과 예시는 다음과 같다.

```
[5, 6, 13, 28, 32, 41]
```

중복된 값은 저장되지 않는 HashSet의 성질을 이용해서 로또번호를 만드는 예제이다. Math.random()을 사용했기 때문에 실행할 때마다 결과가 다를 것이다.

번호를 크기 순으로 정렬하기 위해서 Collections클래스의 sort(List list)를 사용했다. 이 메서드는 인자로 List인터페이스 타입을 필요로 하기 때문에 LinkedList클래스의 생성자 LinkedList(Collection c)를 이용해서 HashSet에 저장된 객체들은 LinkedList에 담아서 처리했다. 

실행 결과의 정렬 기준은, 컬렉션에 저장된 객체가 Integer이기 때문에 Integer클래스에 정의된 기본 정렬이 사용되었다.

(Collection은 인터페이스이고 Collections은 클래스임에 주의하자.)


## HashSet 예제 3

```java
import java.util.*;

class Ex11_11 {
	public static void main(String[] args) {
		HashSet set = new HashSet();

		set.add("abc");
		set.add("abc");
		set.add(new Person("David",10));
		set.add(new Person("David",10));

		System.out.println(set);
	}
}

class Person {
	String name;
	int age;

	Person(String name, int age) {
		this.name = name;
		this.age = age;
	}

	public String toString() {
		return name +":"+ age;
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
[abc, David:10, David:10]
```

Person클래스는 name과 age를 멤버변수로 갖는다. 이름(name)과 나이(age)가 같으면 같은 사람으로 인식하도록 하려는 의도로 작성했다. 하지만 실행 결과를 보면 두 인스턴스의 name과 age의 값이 같음에도 불구하고 서로 다른 것으로 인식하여 'David:10'이 두 번 출력되었다. 클래스의 작성 의도대로 이 두 인스턴스를 같은 것으로 인식하게 하려면 어떻개 해야 할까? Person클래스에 아래의 두 메서드를 추가(오버라이딩)해야 한다. 

```java
public boolean equals(Object obj) {
  if(!(obj instanceof Person)) return false;
  Person p = (Person)obj;
  return name.equals(p.name) && age==p.age;
}

public int hashCode() {
  return Objects.hash(name, age);
}
```

이렇게 오버라이딩 한 후의 출력 결과는 다음과 같다.

```
[David:10, abc]
```

HashSet의 add메서드는 새로운 요소를 추가하기 전에 기존에 저장된 요소와 같은 것인지 판별하기 위해 추가하려는 요소의 equals()와 hashCode()를 호출하기 때문에 이처럼 equals()뿐만 아니라 hashCode()도 목적에 맞게 오버라이딩해야 한다. 


## HashSet 예제 4

```java
import java.util.*;

class Ex11_12 {
	public static void main(String args[]) {
		HashSet setA   = new HashSet();
		HashSet setB   = new HashSet();
		HashSet setHab = new HashSet();
		HashSet setKyo = new HashSet();
		HashSet setCha = new HashSet();

		setA.add("1");	 setA.add("2");  setA.add("3");
		setA.add("4");   setA.add("5");
		System.out.println("A = "+setA);

		setB.add("4");	 setB.add("5");  setB.add("6");		
                setB.add("7");   setB.add("8");
		System.out.println("B = "+setB);

		Iterator it = setB.iterator();
		while(it.hasNext()) {
			Object tmp = it.next();
			if(setA.contains(tmp))
				setKyo.add(tmp);
		}

		it = setA.iterator();
		while(it.hasNext()) {
			Object tmp = it.next();
			if(!setB.contains(tmp))
				setCha.add(tmp);
		}

		it = setA.iterator();
		while(it.hasNext())
			setHab.add(it.next());

		it = setB.iterator();
		while(it.hasNext())
			setHab.add(it.next());

		System.out.println("A ∩ B = " + setKyo);
		System.out.println("A U B = " + setHab);
		System.out.println("A - B = " + setCha); 
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
A = [1, 2, 3, 4, 5]
B = [4, 5, 6, 7, 8]
A ∩ B = [4, 5]
A U B = [1, 2, 3, 4, 5, 6, 7, 8]
A - B = [1, 2, 3]
```
