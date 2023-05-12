---
layout: post
title: 문자열을 숫자로 변환하기
description: Java의 정석 기초편 ch. 9 java.lang패키지와 유용한 클래스
categories: Java
date: 2023-05-12 13:13:00 +09:00
---
다음은 문자열을 숫자로 변환하는 다양한 방법을 보여준다. 문자열을 숫자로 변환할 때는 아래의 방법 중 하나를 선택해서 사용하면 된다.

```java
int i = new Integer("100").intValue();    // floatValue(), longValue(), ...
int i2 = Integer.parseInt("100");         // 주로 이 방법을 많이 사용
int i3 = Integer.valueOf("100");
```

아래의 표는 래퍼 클래스의 '타입.parse타입(String s)' 형식의 메서드와 '타입.valueOf()'메서드를 정리한 것이다. 둘 다 문자를 숫자로 바꿔주는 일을 하지만, 전자는 반환값이 기본형(primitive type)이고 후자는 반환값이 래퍼 클래스 타입이라는 차이가 있다.

<table>
  <tr>
    <th>문자열 -> 기본형</th>
    <th>문자열 -> 래퍼 클래스</th>
  </tr>
  <tr>
    <td>byte b = Byte.parseByte("100");<br>short s = Short.parseShort("100");<br>int i = Integer.parseInt("100");<br>long l = Long.parseLong("100");<br>float f = Float.parseFloat("3.14");<br>double d = Double.parseDouble("3.14");</td>
    <td>Byte b = Byte.valueOf("100");<br>Short s = Short.valueOf("100");<br>Integer i = Integer.valueOf("100");<br>Long l = Long.valueOf("100");<br>Float f = Float.valueOf("3.14");<br>Double d = Double.valueOf("3.14");</td>
  </tr>
</table>

문자열이 10진수가 아닌 다른 진법(radix)의 숫자일 때에도 변환이 가능하도록 다음과 같은 메서드가 제공된다.

```java
static int parseInt(String s, int radix)    // 문자열 s를 radix진법으로 인식
static Integer valueOf(String s, int radix)
```

문자열 "100"이 2진법의 숫자라면 10진수로 4이고, 8진법의 숫자라면 10진수로 64이고, 16진법의 숫자라면 10진수로 256이 된다. 참고로 2진수 100은 100(2)와 같이 표기한다.

```java
int i4 = Integer.parseInt("100", 2);    // 100(2) -> 4
int i5 = Integer.parseInt("100", 8);    // 100(8) -> 64
int i6 = Integer.parseInt("100", 16);   // 100(16) -> 256
int i7 = Integer.parseInt("FF", 8);     // FF(16) -> 255
// int i8 = Integer.parseInt("FF");     // NumberFormatException발생
```

16진법에서는 'A~F'의 문자도 허용하므로 "Integer.parseInt("FF", 16)'와 같은 코드가 가능하지만, 진법을 생략하면 10진수로 간주하기 때문에 'Integer.parseInt("FF")'에서는 NumberFormatException이 발생한다.


## 문자열을 숫자로 변환하기 예제

```java
class Ex9_15 {
	public static void main(String[] args) {
		int		i  = new Integer("100").intValue();
		int		i2 = Integer.parseInt("100");
		Integer  i3 = Integer.valueOf("100");

		int i4 = Integer.parseInt("100",2);
		int i5 = Integer.parseInt("100",8);
		int i6 = Integer.parseInt("100",16);
		int i7 = Integer.parseInt("FF", 16);
//		int i8 = Integer.parseInt("FF");     // NumberFormatException발생 

		Integer i9 = Integer.valueOf("100",2);
		Integer i10 = Integer.valueOf("100",8);
		Integer i11 = Integer.valueOf("100",16);
		Integer i12 = Integer.valueOf("FF",16);
//		Integer i13 = Integer.valueOf("FF"); // NumberFormatException발생 

		System.out.println(i);
		System.out.println(i2);
		System.out.println(i3);
		System.out.println("100(2) -> "+i4);
		System.out.println("100(8) -> "+i5);
		System.out.println("100(16)-> "+i6);
		System.out.println("FF(16) -> "+i7);

		System.out.println("100(2) -> "+i9);
		System.out.println("100(8) -> "+i10);
		System.out.println("100(16)-> "+i11);
		System.out.println("FF(16) -> "+i12);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
100
100
100
100(2) -> 4
100(8) -> 64
100(16)-> 256
FF(16) -> 255
100(2) -> 4
100(8) -> 64
100(16)-> 256
FF(16) -> 255
```
