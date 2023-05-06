---
layout: post
title: 문자열과 기본형 간의 변환
description: Java의 정석 기초편 ch. 9 java.lang패키지와 유용한 클래스
categories: Java
date: 2023-05-06 16:28:00 +09:00
---
숫자로 이루어진 문자열을 숫자로, 또는 그 반대로 변환하는 경우가 자주 있다. 기본형을 문자열로 변경하는 방법은 간단하다. 숫자에 빈 문자열 "" 을 더해주기만 하면 된다. 이 외에도 valueOf()를 사용하는 방법도 있다. 성능은 valueOf()가 더 좋지만, 빈 문자열을 더하는 방법이 간단하고 편하기 때문에 성능 향상이 필요한 때에만 valueOf()를 사용하는 것을 권장한다. 

```java
int i = 100;
String str1 = i + "";               // 100을 "100"으로 변환하는 방법 1
String str2 = String.valueOf(i);    // 100을 "100"으로 변환하는 방법 2
```

(참조변수에 String을 더하면, 참조변수가 가리키는 인스턴스의 toString()을 호출하여 String을 얻은 다음 결합한다.)

반대로 String을 기본형으로 변환하는 방법도 간단하다. valueOf()를 쓰거나 parseInt()를 사용하면 된다.

```java
int i = Integer.parseInt("100");    // "100"을 100으로 변환하는 방법 1
int i2 = Integer.valueOf("100");    // "100"을 100으로 변환하는 방법 2
```

원래 valueOf()의 반환 타입은 int가 아니라 Integer인데, 오토박싱(auto-boxing)에 의해 Integer가 int로 자동 변환된다.

```java
Integer i2 = Integer.valueOf("100");    // 원래는 반환 타입이 Integer
```

예전에는 parseInt()와 같은 메서드를 많이 썼는데, 메서드의 이름을 통일하기 위해 valueOf()가 나중에 추가되었다. valueOf(String s)는 메서드 내부에서 그저 parseInt(String s)를 호출할 뿐이므로, 두 메서드는 반환 타입만 다르지 같은 메서드이다. 

```java
public static Integer valueOf(String s) throws NumberFormatException {
  return Integer.valueOf(parseInt(s, 10));    // 여기서 10은 10진수를 의미함
}
```

<table>
  <tr>
    <th>기본형 -> 문자열</th>
    <th>문자열 -> 기본형</th>
  </tr>
  <tr>
    <td>String String.valueOf(boolean b)<br>String String.valueOf(char c)<br>String String.valueOf(int i)<br>String String.valueOf(long l)<br>String String.valueOf(float f)<br>String String.valueOf(double d)</td>
    <td>boolean Boolean.parseBoolean(String s)<br>byte Byte.parseByte(String s)<br>short Short.parseShort(String s)<br>int Integer.parseInt(String s)<br>long Long.parseLong(String s)<br>float Float.parseFloat(String s)<br>double Double.parseDouble(String s)</td>
  </tr>
</table>

(byte, short을 문자열로 변경할 때는 String valueOf(int i)를 사용하면 된다.)


### 문자열과 기본형 간의 변환 예제

```java
class Ex9_10 {
	public static void main(String[] args) {
		int iVal = 100;
		String strVal = String.valueOf(iVal); // int를 String으로 변환한다.
		
		double dVal = 200.0;
		String strVal2 = dVal + "";           // int를 String으로 변환하는 또 다른 방법 

		double sum  = Integer.parseInt("+"+strVal) + Double.parseDouble(strVal2);
		double sum2 = Integer.valueOf(strVal) + Double.valueOf(strVal2);
		
		System.out.println(String.join("",strVal,"+",strVal2,"=")+sum);
		System.out.println(strVal+"+"+strVal2+"="+sum2);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
100+200.0=300.0
100+200.0=300.0
```

이 예제는 문자열과 기본형 간 변환의 예를 보여준다. parseInt()나 parseFloat() 같은 메서드는 문자열에 공백 또는 문자가 포함되어 있는 경우 변환 시 예외(NumberFormatException)가 발생할 수 있으므로 주의해야 한다. 그래서 문자열 양 끝의 공백을 제거해주는 trim()을 습관적으로 같이 사용하기도 한다. 

```java
int val = Integer.parseInt(" 123 ".trim());   // 문자열 양 끝의 공백을 제거 후 변환
```

그러나 부호를 의미하는 '+'나 소수점을 의미하는 '.'와 float형 값을 뜻하는 f와 같은 자료형 접미사는 허용된다. 단, 자료형에 알맞은 변환을 하는 경우에만 허용된다.

만일 '1.0f'를 int형 변환 메서드인 Integer.parseInt(String s)를 사용해서 변환하려 하면 예외가 발생하지만, Float.parseFloat(String s)를 사용하면 아무런 문제가 없다.

이처럼 문자열을 숫자로 변환하는 과정에서는 예외가 발생하기 쉽기 때문에 주의를 기울여야 하고, 예외가 발생했을 때의 처리를 적절히 해주어야 한다.

(Integer클래스의 static int parseInt(String s, int radix) 를 사용하면 16진수 값으로 표현된 문자열도 변환할 수 있기 때문에 대소문자 구별 없이 a, b, c, d, e, f도 사용할 수 있다. int result = Integer.parseInt("a", 16); 의 경우 result에는 정수값 10이 저장된다. 16진수 a는 10진수로는 10을 뜻한다.)
