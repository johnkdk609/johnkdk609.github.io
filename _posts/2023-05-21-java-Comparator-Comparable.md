---
layout: post
title: Comparator와 Comparable
description: Java의 정석 기초편 ch. 11 컬렉션 프레임웍
categories: Java
date: 2023-05-21 23:07:00 +09:00
---
Comparator와 Comparable은 모두 인터페이스로 컬렉션을 정의하는 데 필요한 메서드를 정의하고 있으며, Comparable을 구현하고 있는 클래스들은 같은 타입의 인스턴스끼리 서로 비교할 수 있는 클래스들, 주로 Integer와 같은 wrapper클래스와 String, Date, File과 같은 것들이다. 기본적으로 오름차순, 즉 작은 값에서부터 큰 값의 순으로 정렬되도록 구현되어 있다. 그래서 Comparable을 구현한 클래스는 정렬이 가능하다는 것을 의미한다.

참고로 Java API문서에서 Comparable을 찾아보면, 이를 구현한 클래스의 목록을 볼 수 있다. Comparator와 Comparable의 실제 소스는 다음과 같다.

```java
public interface Comparator {
  int compare(Object o1, Object o2);    // o1과 o2를 비교
  boolean equals(Object obj);
}

public interface Comparable {
  int compareTo(Object o);    // 객체 자신(this)과 o를 비교
}
```

(Comparable은 java.lang패키지에 있고, Comparator은 java.util패키지에 있다.)

compare()와 compareTo()는 선언 형태와 이름이 약간 다를 뿐 두 객체를 비교한다는 같은 기능을 목적으로 고안된 것이다. compareTo()의 반환값은 int이지만 실제로는 비교하는 두 객체가 같으면 0, 비교하는 값보다 작으면 음수, 크면 양수를 반환하도록 구현해야 한다. 이와 마찬가지로 compare()도 객체를 비교해서 음수, 0, 양수 중의 하나를 반환하도록 구현해야 한다. 

> **Comparable** - 기본 정렬 기준을 구현하는 데 사용\
> **Comparator** - 기본 정렬 기준 외에 다른 기준으로 정렬하고자 할 때 사용

equals메서드는 모든 클래스가 가지고 있는 공통적인 메서드이지만, Comparator를 구현하는 클래스는 오버라이딩이 필요할 수도 있다는 것을 알리기 위해서 정의한 것일 뿐, 그냥 compare(Object o1, Object o2)만 구현하면 된다.


## Comparator와 Comparable 예제 코드

```java
import java.util.*;

class Ex11_7 {
	public static void main(String[] args) {
		String[] strArr = {"cat", "Dog", "lion", "tiger"};

		Arrays.sort(strArr); // String의 Comparable구현에 의한 정렬 
		System.out.println("strArr=" + Arrays.toString(strArr));

		Arrays.sort(strArr, String.CASE_INSENSITIVE_ORDER); // 대소문자 구분 안 함 
		System.out.println("strArr=" + Arrays.toString(strArr));

		Arrays.sort(strArr, new Descending()); // 역순 정렬 
		System.out.println("strArr=" + Arrays.toString(strArr));
	}
}

class Descending implements Comparator { 
	public int compare(Object o1, Object o2){
		if( o1 instanceof Comparable && o2 instanceof Comparable) {
			Comparable c1 = (Comparable)o1;
			Comparable c2 = (Comparable)o2;
			return c1.compareTo(c2) * -1 ; // -1을 곱해서 기본 정렬방식의 역으로 변경한다. 
                        // 또는 c2.compareTo(c1)와 같이 순서를 바꿔도 된다. 
		}
		return -1;
	} 
}
```

위 코드의 출력 결과는 다음과 같다.

```
strArr=[Dog, cat, lion, tiger]
strArr=[cat, Dog, lion, tiger]
strArr=[tiger, lion, cat, Dog]
```

Arrays.sort()는 배열을 정렬할 때, Comparator를 지정해주지 않으면 저장하는 객체(Comparable을 구현한 클래스의 객체)에 구현된 내용에 따라 정렬된다.

```java
static void sort(Object[] a)    // 객체 배열에 저장된 객체가 구현한 Comparable에 의한 정렬
static void sort(Object[] a, Comparator c)    // 지정한 Comparator에 의한 정렬
```

String의 Comparable 구현은 문자열이 사전 순으로 정렬되도록 작성되어 있다. 문자열의 오름차순 정렬은 공백, 숫자, 대문자, 소문자의 순으로 정렬되는 것을 의미한다. 정확히 얘기하면 문자의 유니코드의 순서가 작은 값에서부터 큰 값으로 정렬되는 것이다. 그리고 아래와 같이 대소문자를 구분하지 않고 비교하는 Comparator를 상수의 형태로 제공한다. 

```java
public static final Comparator CASE_INSENSITIVE_ORDER
```

이 Comparator를 이용하면, 문자열을 대소문자 구분 없이 정렬할 수 있다.

```java
Arrays.sort(strArr, String.CASE_INSENSITIVE_ORDER);   // 대소문자 구분 없이 정렬
```


## Integer와 Comparable

아래의 코드는 Integer클래스의 일부인데, Comparable의 compareTo(Object o)를 구현해 놓은 것을 볼 수 있다.

```java
public final class Integer extends Number implements Comparable {
    ...
  public int compareTo(Object o) {
    return compareTo((Integer)o);
  }
  
  public int compareTo(Integer anotherInteger) {
    int thisVal = this.value;
    int anotherVal = anotherInteger.value;
    
    // 비교하는 값이 크면 -1, 같으면 0, 작으면 1을 반환한다.
    return (thisVal < anotherVal ? -1 : (thisVal == anotherVal ? 0 : 1));
  }
    ...
}
```

Integer클래스의 compareTo()는 두 Integer객체에 저장된 int값(value)을 비교해서 같으면 0, 크면 -1, 작으면 1을 반환한다.

Comparable을 구현한 클래스들이 기본적으로 오름차순으로 정렬되어 있지만, 내림차순으로 정렬한다든가 아니면 다른 기준에 의해서 정렬되도록 하고 싶을 때 Comparator을 구현해서 정렬 기준을 제공할 수 있다.

위의 코드에서는 보다 나은 성능을 위해 삼항 연산자를 사용했지만, 대부분의 경우 아래와 같이 간단한 뺄셈만으로 compareTo()를 구현할 수 있다.

```java
// Arrays.sort()와 같은 메서드가 정렬을 수행하는 과정에서, compareTo()를 호출한다.
public int compareTo(Integer anotherInteger) {
  int thisVal = this.value;
  int anotherVal = anotherInteger.value;
  
  // 왼쪽 값이 작으면 음수를, 두 값이 같으면 0, 왼쪽 값이 크면 양수를 반환한다.
  return thisVal - anotherVal;    // 내림차순의 경우 반대로 뺄셈하면 된다.
}
```

Arrays.sort()와 같은 메서드의 정렬 알고리즘은 이미 훌륭하게 잘 작성되어 있으므로, 우리가 해야할 일은 위와 같이 compareTo()를 구현해서 어떤 비교 기준으로 정렬할지만 알려주는 것으로 충분하다.


## Integer와 Comparable 예제 코드

```java
import java.util.*; 

class Ex11_8 { 
	public static void main(String[] args) { 
		Integer[] arr = { 30, 50, 10, 40, 20 }; 

		Arrays.sort(arr); // Integer가 가지고 있는 기본 정렬기준 compareTo()로 정렬 
		System.out.println(Arrays.toString(arr));

		// sort(Object[] objArr, Comparator c)
		Arrays.sort(arr, new DescComp()); // DescComp에 구현된 정렬 기준으로 정렬
		System.out.println(Arrays.toString(arr));
	} // main
}	

class DescComp implements Comparator {
	public int compare(Object o1, Object o2) {
		if(!(o1 instanceof Integer && o2 instanceof Integer))
			return -1; // Integer가 아니면, 비교하지 않고 -1 반환

		Integer i  = (Integer)o1;
		Integer i2 = (Integer)o2;
		
		// return i2 - i; 또는 return i2.compareTo(i);도 가능
		return i.compareTo(i2) * -1; // 기본 정렬인 compareTo()의 역순으로 정렬
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
[10, 20, 30, 40, 50]
[50, 40, 30, 20, 10]
```

정렬할 때는 항상 정렬 기준이 필요하다. Arrays.sort()로 정렬할 때 아무런 정렬 기준을 주지 않았는데도 정렬이 되는 이유는 배열 arr에 저장된 Integer가 내부에 정렬 기준을 가지고 있기 때문이다.

```java
Arrays.sort(arr);   // Integer가 가지고 있는 기본 정렬 기준 compareTo()로 정렬
```

정렬할 때는 아래와 같이 정렬 기준을 매개변수로 제공하든가 아니면 위와 같이 정렬 대상에 저장된 객체가 정렬 기준을 가지고 있어야 한다. 그렇지 않으면 예외가 발생한다.

```java
Arrays.sort(arr, new DescComp());   // DescComp의 compare()로 정렬
```

정렬 기준이라는 것은 단순히 양수, 0, 음수 중에서 하나를 반환하도록 작성된 메서드라서, 그저 -1을 곱하기만 하면 반대로 정렬된 결과를 얻을 수 있다.