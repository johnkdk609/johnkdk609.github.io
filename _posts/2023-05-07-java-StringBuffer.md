---
layout: post
title: StringBuffer클래스
description: Java의 정석 기초편 ch. 9 java.lang패키지와 유용한 클래스
categories: Java
date: 2023-05-07 21:25:00 +09:00
---
String클래스는 인스턴스를 생성할 때 지정된 문자열을 변경할 수 없지만, **StringBuffer클래스는 변경이 가능**하다. 내부적으로 문자열 편집을 위한 버퍼(buffer)를 가지고 있으며, StringBuffer인스턴스를 생성할 때 그 크기를 지정할 수 있다. 

이때, **편집할 문자열의 길이를 고려하여 버퍼의 길이를 충분히 잡아주는 것이 좋다.** 편집중인 문자열이 버퍼의 길이를 넘어서게 되면 버퍼의 길이를 늘려주는 작업이 추가로 수행되어야 하기 때문에 작업 효율이 떨어진다.

StringBuffer클래스는 String클래스와 유사한 점이 많다. 아래의 코드에서 알 수 있듯이, StringBuffer클래스는 String클래스와 같이 **문자열을 저장하기 위한 char형 배열의 참조변수를 인스턴스변수로 선언해 놓고 있다.** StringBuffer인스턴스가 생성될 때, char형 배열이 생성되며 이때 생성된 char형 배열을 인스턴스변수 value가 참조하게 된다. 

```java
public final class StringBuffer implements java.io.Serializable {
  private char[] value;
    ...
}
```


## StringBuffer의 생성자

**StringBuffer클래스의 인스턴스를 생성할 때, 적절한 길이의 char형 배열이 생성되고, 이 배열은 문자열을 저장하고 편집하기 위한 공간(buffer)으로 사용된다.**

StringBuffer인스턴스를 생성할 때는 생성자 StringBuffer(int length)를 사용해서 StringBuffer인스턴스에 저장될 문자열의 길이를 고려하여 충분히 여유 있는 크기로 지정하는 것이 좋다. StringBuffer인스턴스를 생성할 때, 버퍼의 크기를 지정해주지 않으면 16개의 문자를 저장할 수 있는 크기의 버퍼를 생성한다. 

```java
public StringBuffer(int length) {
  value = new char[length];
  shared = false;
}

public StringBuffer() {
  this(16);     // 버퍼의 크기를 지정하지 않으면 버퍼의 크기는 16이 된다.
}

public StringBuffer(String str) {
  this(str.length() + 16);    // 지정한 문자열의 길이보다 16이 더 크게 버퍼를 생성한다.
  append(str);
}
```

아래의 코드는 StringBuffer클래스의 일부인데, 버퍼의 크기를 변경하는 내용의 코드이다.

StringBuffer인스턴스로 문자열을 다루는 작업을 할 때, 버퍼의 크기가 작업하려는 문자열의 길이보다 작을 때는 내부적으로 버퍼의 크기를 증가시키는 작업이 수행된다.

배열의 길이는 변경될 수 없으므로 새로운 길이의 배열을 생성한 후에 이전 배열의 값을 복사해야 한다.

```java
// 새로운 길이(newCapacity)의 배열을 생성한다. newCapacity는 정수값이다.
char newValue[] = new char[newCapacity];

// 배열 value의 내용을 배열 newValue로 복사한다.
System.arraycopy(value, 0, newValue, 0, count);   // count는 문자열의 길이
value = newValue;   // 새로 생성된 배열의 주소를 참조변수 value에 저장
```

이렇게 함으로써 StringBuffer클래스의 인스턴스변수 value는 길이가 증가된 새로운 배열을 참조하게 된다.


## StringBuffer의 변경

String과 달리 StringBuffer는 내용을 변경할 수 있다. 예를 들어 아래와 같이 StringBuffer를 생성하였다고 가정하자.

```java
StringBuffer sb = new StringBuffer("abc");
```

<img width="600" alt="image" title="[자바의 정석 - 기초편] ch9-15~18 StringBuffer클래스" src="https://user-images.githubusercontent.com/88493727/236678404-bee5c25e-1f8b-4b53-ba1a-d586e9dc7037.png">

그리고 sb에 문자열 "123"을 추가하면,

```java
sb.append("123");   // sb의 내용 뒤에 "123"을 추가한다.
```

<img width="600" alt="image" title="[자바의 정석 - 기초편] ch9-15~18 StringBuffer클래스" src="https://user-images.githubusercontent.com/88493727/236678646-25fdaa44-a695-4a00-8cb8-9ad4888fc44c.png">

append()는 반환 타입이 StringBuffer인데 자신의 주소를 반환한다. 그래서 아래와 같은 문장이 수행되면, sb에 새로운 문자열이 추가되고 sb 자신의 주소를 반환하여 sb2에는 sb의 주소인 0x100이 저장된다. 

```java
StringBuffer sb2 = sb.append("ZZ");
System.out.println(sb);   // abc123ZZ
System.out.println(sb2);  // abc123ZZ
```

sb와 sb2가 모두 같은 StringBuffer인스턴스를 가리키고 있으므로 같은 내용이 출력된다. 그래서 하나의 StringBuffer인스턴스에 대해 아래와 같이 연속적으로 append()를 호출하는 것이 가능하다.

```java
StringBuffer sb = new StringBuffer("abc");
sb.append("123");
sb.append("ZZ");
```

위 코드를 아래와 같이 변경할 수 있다.

```java
StringBuffer sb = new StringBuffer("abc");
sb.append("123").append("ZZ");
```

만일 append()의 반환타입이 void라면 이렇게 할 수 없었을 것이다. StringBuffer클래스에는 append()처럼 객체 자신을 반환하는 메서드들이 많이 있다.


## StringBuffer의 비교

String클래스에서는 equals메서드를 오버라이딩 해서 문자열의 내용을 비교하도록 구현되어 있지만, StringBuffer클래스는 equals메서드를 오버라이딩하지 않아서 StringBuffer클래스의 equals메서드를 사용해도 등가비교연산자(==)로 비교한 것과 같은 결과를 얻는다. 

```java
StringBuffer sb = new StringBuffer("abc");
StringBuffer sb2 = new StringBuffer("abc");

System.out.println(sb == sb2);        // false
System.out.println(sb.equals(sb2));   // false
```

반면에 toString()은 오버라이딩 되어 있어서 StringBuffer인스턴스에 toString()을 호출하면, 담고 있는 문자열을 String으로 반환한다.

그래서 StringBuffer인스턴스에 담긴 문자열을 비교하기 위해서는 StringBuffer인스턴스에 toString()을 호출해서 String인스턴스를 얻은 다음, 여기에 equals메서드를 사용해서 비교해야 한다.

```java
String s = sb.toString();           // StringBuffer를 String으로 변환
String s2 = sb2.toString();

System.out.println(s.equals(s2));   // true
```

```java
class Ex9_11 {
	public static void main(String[] args) {
		StringBuffer sb  = new StringBuffer("abc");
		StringBuffer sb2 = new StringBuffer("abc");

		System.out.println("sb == sb2 ? " + (sb == sb2));
		System.out.println("sb.equals(sb2) ? " + sb.equals(sb2));
		
		// StringBuffer의 내용을 String으로 변환한다. 
		String s  = sb.toString();    // String s = new String(sb);와 같다. 
		String s2 = sb2.toString();

		System.out.println("s.equals(s2) ? " + s.equals(s2));
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
sb == sb2 ? false
sb.equals(sb2) ? false
s.equals(s2) ? true
```
