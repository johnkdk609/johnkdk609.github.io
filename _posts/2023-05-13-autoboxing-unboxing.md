---
layout: post
title: 오토박싱 & 언박싱
description: Java의 정석 기초편 ch. 9 java.lang패키지와 유용한 클래스
categories: Java
date: 2023-05-13 19:07:00 +09:00
---
JDK1.5 이전에는 기본형과 참조형 간의 연산이 불가능했기 때문에, 래퍼 클래스로 기본형을 객체로 만들어서 연산해야 했다.

```java
int i = 5;
Integer iObj = new Integer(7);

int sum = i + iObj;   // 에러. 기본형과 참조형 간의 덧셈 불가(JDK1.5 이전)
```

그러나 이제는 기본형과 참조형 간의 덧셈이 가능하다. 자바 언어의 규칙이 바뀐 것은 아니고, 컴파일러가 자동으로 변환하는 코드를 넣어주기 때문이다. 아래의 경우, 컴파일러가 Integer객체를 int타입의 값으로 변환해주는 intValue()를 추가해준다.

<table>
  <tr>
    <th>컴파일 전의 코드</th>
    <th>컴파일 후의 코드</th>
  </tr>
  <tr>
    <td>int i = 5;<br>Integer iObj = new Integer(7);<br><br>int sum = i + iObj;</td>
    <td>int i = 5;<br>Integer iObj = new Integer(7);<br><br>int sum = i + iObj.intValue();</td>
  </tr>
</table>

이 외에도 내부적으로 객체 배열을 가지고 있는 Vector클래스나 ArrayList클래스에 기본형 값을 저장해야할 때나 형변환이 필요할 때도 컴파일러가 자동적으로 코드를 추가해준다. **기본형 값을 래퍼 클래스의 객체로 자동 변환해주는 것을 '오토박싱(autoboxing)'이라고 하고, 반대로 변환하는 것을 '언박싱(unboxing)'이라고 한다.**

```java
ArrayList<Integer> list = new ArrayList<Integer>();
list.add(10);               // 오토박싱. 10 -> new Integer(10)

int value = list.get(0);    // 언박싱. new Integer(10) -> 10
```

위의 코드에서 알 수 있듯이 ArrayList에 숫자를 저장하거나 꺼낼 때, 기본형 값을 래퍼 클래스의 객체로 변환하지 않아도 되므로 편리하다.


## 오토박싱 & 언박싱 예제 코드

```java
class Ex9_16 {
	public static void main(String[] args) {
		int i = 10;

		// 기본형을 참조형으로 형변환 (형변환 생략 가능) 
		Integer intg = (Integer)i; // Integer intg = Integer.valueOf(i);
		Object obj = (Object)i;    // Object obj = (Object)Integer.valueOf(i);

		Long lng = 100L;  // Long lng = new Long(100L);

		int i2 = intg + 10;   // 참조형과 기본형 간의 연산 가능 
		long l = intg + lng;  // 참조형 간의 덧셈도 가능 

		Integer intg2 = new Integer(20);
		int i3 = (int)intg2;  // 참조형을 기본형으로 형변환도 가능 (형변환 생략 가능) 

		Integer intg3 = intg2 + i3; 

		System.out.println("i = "+i);
		System.out.println("intg = "+intg);
		System.out.println("obj = "+obj);
		System.out.println("lng = "+lng);
		System.out.println("intg + 10 = "+i2);
		System.out.println("intg + lng = "+l);
		System.out.println("intg2 = "+intg2);
		System.out.println("i3 = "+i3);
		System.out.println("intg2 + i3 = "+intg3);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```java
i = 10
intg = 10
obj = 10
lng = 100
intg + 10 = 20
intg + lng = 110
intg2 = 20
i3 = 20
intg2 + i3 = 40
```

이 예제는 오토박싱(autoboxing)을 이용해서 기본형과 참조형 간의 형변환과 연산을 수행하는 예를 보여준다.

사실 이 기능은 컴파일러가 제공하는 편리한 기능일 뿐 자바의 원칙이 바뀐 것은 아니다. 생성자가 없는 클래스에 컴파일러가 기본 생성자를 자동적으로 추가해 주듯이 개발자가 간략하게 쓴 구문을 컴파일러가 원래의 구문으로 변경해주는 것뿐이다.

<table>
  <tr>
    <th>컴파일 전의 코드</th>
    <th>컴파일 후의 코드</th>
  </tr>
  <tr>
    <td>Integer intg = (Integer)i;<br>Object obj = (Object)i;<br>Long lng = 100L;</td>
    <td>Integer intg = Integer.valueOf(i);<br>Object obj = (Object)Integer.valueOf(i);<br>Long lng = new Long(100L);</td>
  </tr>
</table>

























