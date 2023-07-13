---
layout: post
title: 래퍼(wrapper) 클래스
description: Java의 정석 기초편 ch. 9 java.lang패키지와 유용한 클래스
categories: Java
date: 2023-05-10 11:44:00 +09:00
---
객체지향 개념에서 모든 것은 객체로 다루어져야 한다. 그러나 자바에서는 8개의 기본형을 객체로 다루지 않는데 이것이 바로 자바가 완전한 객체지향 언어가 아니라는 얘기를 듣는 이유이다. 그 대신 보다 높은 성능을 얻을 수 있다.

때로는 기본형(primitive type) 변수도 어쩔 수 없이 객체로 다뤄야 하는 경우가 있다. 예를 들면, 매개변수로 객체를 요구할 때, 기본형 값이 아닌 객체로 저장해야할 때, 객체 간의 비교가 필요할 때 등등의 경우에는 기본형 값들을 객체로 변환하여 작업을 수행해야 한다. 

**이때 사용되는 것이 래퍼(wrapper) 클래스이다. 8개의 기본형을 대표하는 8개의 래퍼클래스가 있는데, 이 클래스들을 이용하면 기본형 값을 객체로 다룰 수 있다.**

```java
public final class Integer extends Number implements Comparable {
    ...
  private int value;
    ...
}
```

이처럼 래퍼 클래스들은 객체 생성 시에 생성자의 인자로 주어진 각 자료형에 알맞은 값을 내부적으로 저장하고 있으며, 이에 관련된 여러 메서드가 정의되어 있다.

<table>
	<tr>
		<th>기본형</th>
		<th>래퍼클래스</th>
		<th>생성자</th>
		<th>활용 예</th>
	</tr>
	<tr>
		<td>boolean</td>
		<td>Boolean</td>
		<td>Boolean(boolean value)<br>Boolean(String s)</td>
		<td>Boolean b = new Boolean(true);<br>Boolean b2 = new Boolean("true");</td>
	</tr>
	<tr>
		<td>char</td>
		<td>Character</td>
		<td>Character(char value)</td>
		<td>Character c = new Character('a');</td>
	</tr>
	<tr>
		<td>byte</td>
		<td>Byte</td>
		<td>Byte(byte value)<br>Byte(String s)</td>
		<td>Byte b = new Byte(10);<br>Byte b2 = new Byte("10");</td>
	</tr>
	<tr>
		<td>short</td>
		<td>Short</td>
		<td>Short(short value)<br>Short(String s)</td>
		<td>Short s = new Short(10);<br>Short s2 = new Short("10");</td>
	</tr>
	<tr>
		<td>int</td>
		<td>Integer</td>
		<td>Integer(int value)<br>Integer(String s)</td>
		<td>Integer i = new Integer(100);<br>Integer i2 = new Integer("100");</td>
	</tr>
	<tr>
		<td>long</td>
		<td>Long</td>
		<td>Long(long value)<br>Long(String s)</td>
		<td>Long l = new Lone(100);<br>Long l2 = new Long("100");</td>
	</tr>
	<tr>
		<td>float</td>
		<td>Float</td>
		<td>Float(double value)<br>Float(float value)<br>Float(String s)</td>
		<td>Float f = new Float(1.0);<br>Float f2 = new Float(1.0f);<br>Float f3 = new Float("1.0f");</td>
	</tr>
	<tr>
		<td>double</td>
		<td>Double</td>
		<td>Double(double value)<br>Double(String s)</td>
		<td>Double d = new Double(1.0);<br>Double d2 = new Double("1.0");</td>
	</tr>
</table>

래퍼 클래스의 생성자는 매개변수로 문자열이나 각 자료형의 값들을 인자로 받는다. 이때 **주의해야할 것은 생성자의 매개변수로 문자열을 제공할 때, 각 자료형에 알맞은 문자열을 사용해야 한다는 것**이다. 예를 들어, 'new Integer("1.0");'과 같이 하면 NumberFormatException이 발생한다. 


## 래퍼(wrapper) 클래스 예제

```java
class Ex9_14 {
	public static void main(String[] args) {
		Integer i  = new Integer(100);
		Integer i2 = new Integer(100);

		System.out.println("i==i2 ? "+(i==i2));
		System.out.println("i.equals(i2) ? "+i.equals(i2));
		System.out.println("i.compareTo(i2)="+i.compareTo(i2));
		System.out.println("i.toString()="+i.toString());

		System.out.println("MAX_VALUE="+Integer.MAX_VALUE);
		System.out.println("MIN_VALUE="+Integer.MIN_VALUE);
		System.out.println("SIZE="+Integer.SIZE+" bits");
		System.out.println("BYTES="+Integer.BYTES+" bytes");
		System.out.println("TYPE="+Integer.TYPE);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
i==i2 ? false
i.equals(i2) ? true
i.compareTo(i2)=0
i.toString()=100
MAX_VALUE=2147483647
MIN_VALUE=-2147483648
SIZE=32 bits
BYTES=4 bytes
TYPE=int
```

**래퍼 클래스들은 모두 equals()가 오버라이딩 되어 있어서 주소값이 아닌 객체가 가지고 있는 값을 비교한다.** 그래서 실행 결과를 보면 equals()를 이용한 두 Integer객체의 비교 결과가 true라는 것을 알 수 있다. 오토박싱이 된다고 해도 Integer객체에 비교연산자를 사용할 수 없다. 대신 compareTo()를 제공한다. 

그리고 **toString()도 오버라이딩되어 있어서 객체가 가지고 있는 값을 문자열로 변환하여 반환한다.** 이 외에도 래퍼 클래스들은 MAX_VALUE, MIN_VALUE, SIZE, BYTES, TYPE 등의 static상수를 공통적으로 가지고 있다.
