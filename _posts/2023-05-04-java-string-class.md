---
layout: post
title: String클래스
description: Java의 정석 기초편 ch. 9 java.lang패키지와 유용한 클래스
categories: Java
date: 2023-05-04 11:56:00 +09:00
---
자바에서는 문자열을 위한 String클래스를 제공한다. String클래스는 문자열을 저장하고 이를 다루는 데에 필요한 메서드를 함께 제공한다.

### 변경 불가능한(immutable) 클래스

String클래스에는 문자열을 저장하기 위해서 문자형 배열 참조변수(char\[\]) value를 인스턴스 변수로 정의해 놓고 있다. 인스턴스 생성 시 생성자의 매개변수로 입력 받는 문자열은 이 인스턴스변수(value)에 문자형 배열(char\[\])로 저장되는 것이다.

(String클래스는 앞에 final이 붙어 있으므로 다른 클래스의 조상이 될 수 없다.)

```java
public final class String implements java.io.Serializable, Comparable {
  private char[] value;
    ...
```

**한 번 생성된 String인스턴스가 갖고 있는 문자열은 읽어올 수만 있고, 변경할 수는 없다.**

예를 들어 아래와 같이 '+'연산자를 이용해서 문자열을 결합하는 경우 인스턴스 내의 문자열이 바뀌는 것이 아니라 새로운 문자열("ab")이 담긴 String인스턴스가 생성되는 것이다.

덧셈연산자 '+'를 사용해서 문자열을 결합하는 것은 매 연산 시마다 새로운 문자열을 가진 String인스턴스가 생성되어 메모리 공간을 차지하게 되므로 가능한 한 결합 횟수를 줄이는 것이 좋다.

**문자열 간의 결합이나 추출 등 문자열을 다루는 작업이 많이 필요한 경우에는 String클래스 대신 StringBuffer클래스를 사용하는 것이 좋다.** StringBuffer인스턴스에 저장된 문자열은 변경이 가능하므로 하나의 StringBuffer인스턴스만으로도 문자열을 다루는 것이 가능해진다.


### 문자열(String)의 비교

문자열을 만들 때는 두 가지 방법이 있다.

**1. 문자열 리터럴을 지정하는 방법**\
**2. String클래스의 생성자를 사용해서 만드는 방법**

```java
String str1 = "abc";    // 문자열 리터럴 "abc"의 주소가 str1에 저장됨
String str2 = "abc";    // 문자열 리터럴 "abc"의 주소가 str2에 저장됨
String str3 = new String("abc");    // 새로운 String인스턴스를 생성
String str4 = new String("abc");    // 새로운 String인스턴스를 생성
```

**String클래스의 생성자를 이용한 경우에는 new연산자에 의해서 메모리 할당이 이루어지기 때문에 항상 새로운 String인스턴스가 생성된다. 그러나 문자열 리터럴은 이미 존재하는 것을 재사용하는 것이다.**

즉, 문자열 리터럴의 경우 가령 "abc"이 0x100의 주소에 저장되어 있다면, str1과 str2 모두 0x100을 가리키고 있는 것이다.

str3은 0x200 주소의 "abc"를 가리키고 있고, str4는 0x300 주소의 "abc"를 가리키고 있는 것이라고 할 수 있다.

이 경우, str1 == str2 는 0x100 == 0x100 이므로 true이다. 반면, str3 == str4 의 경우, 0x200 == 0x300 이므로 false이다.

equals()를 사용했을 때는 두 문자열의 내용("abc")을 비교하기 때문에 두 경우 모두 true를 결과로 얻지만, String인스턴스의 주소를 등가비교연산자 '=='로 비교했을 때는 결과가 다르다.

```java
class Ex9_6 {
	public static void main(String[] args) {
		String str1 = "abc";
		String str2 = "abc";
		System.out.println("String str1 = \"abc\";");
		System.out.println("String str2 = \"abc\";");

		System.out.println("str1 == str2 ?  " + (str1 == str2));
		System.out.println("str1.equals(str2) ? " + str1.equals(str2));
		System.out.println();

		String str3 = new String("abc");
		String str4 = new String("abc");

		System.out.println("String str3 = new String(\"abc\");");
		System.out.println("String str4 = new String(\"abc\");");

		System.out.println("str3 == str4 ? " + (str3 == str4));
		System.out.println("str3.equals(str4) ? " + str3.equals(str4));
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
String str1 = "abc";
String str2 = "abc";
str1 == str2 ?  true
str1.equals(str2) ? true

String str3 = new String("abc");
String str4 = new String("abc");
str3 == str4 ? false
str3.equals(str4) ? true
```


### 문자열 리터럴(String 리터럴)

자바 소스파일에 포함된 모든 문자열 리터럴은 컴파일 시에 클래스 파일에 저장된다. 이때 **같은 내용의 문자열 리터럴은 한 번만 저장**된다. 문자열 리터럴도 String인스턴스이고, 한 번 생성하면 내용을 변경할 수 없으니 하나의 인스턴스를 공유하면 되기 때문이다. 

```java
class Ex9_7 {
	public static void main(String args[]) {
		String s1 = "AAA";
		String s2 = "AAA";
		String s3 = "AAA";
		String s4 = "BBB";
	}
}
```

위의 예제를 컴파일 하면 StringEx2.class 파일이 생성된다. String리터럴들은 컴파일 시에 클래스 파일에 저장된다. 위의 예제를 실행하면 "AAA"라는 문자열을 담고 있는 String인스턴스가 하나 생성된 후, 참조변수 s1, s2, s3는 모두 이 String인스턴스를 참조하게 된다. 

<img width="300" alt="[자바의 정석 - 기초편] ch9-7~10 String클래스, 문자열 비교, 빈 문자열" title="[자바의 정석 - 기초편] ch9-7~10 String클래스, 문자열 비교, 빈 문자열" src="https://user-images.githubusercontent.com/88493727/236113106-e1a1966f-c9d0-47ea-9cd9-856671199225.png">

클래스 파일이 클래스 로더에 의해 메모리에 올라갈 때, 클래스 파일의 리터럴들이 JVM 내에 있는 '상수 저장소(constant pool)'에 저장된다. 이때, "AAA"와 같은 문자열 리터럴은 자동적으로 생성되어 저장되는 것이다. 


### 빈 문자열(empty string)

길이가 0인 배열이 존재할 수 있는가? 답은 'Yes'이다. char형 배열도 길이가 0인 배열을 생성할 수 있고, 이 배열을 내부적으로 가지고 있는 문자열이 바로 **빈 문자열**이다.

'String s = "";'과 같은 문장이 있을 때, 참조변수 s가 참조하고 있는 String인스턴스는 내부에 'new char[0]'과 같이 길이가 0인 char형 배열을 저장하고 있는 것이다.

```java
char[] chArr = new char[0];   // 길이가 0인 char배열
int[] iArr = {};              // 길이가 0인 int배열
```

길이가 0이기 때문에 아무런 문자도 저장할 수 없는 배열이라 무의미하게 느껴지겠지만 어쨌든 이러한 표현이 가능하다.

그러나 'String s = "";'과 같은 표현이 가능하다고 해서 'char c = '';'와 같은 표현도 가능한 것은 아니다. **char형 변수에는 반드시 하나의 문자를 지정해야 한다.**

```java
String s = null;
char c = '\u0000';
```

```java
String s = "";    // 빈 문자열로 초기화
char c = ' ';     // 공백으로 초기화
```

일반적으로 변수를 선언할 때, 각 타입의 기본형으로 초기화 하지만 String은 참조형 타입의 기본값인 null보다는 빈 문자열로, char형은 기본값인 '\u0000' 대신 공백으로 초기화하는 것이 보통이다.

```java
class Ex9_8 {
	public static void main(String[] args) {
		// 길이가 0인 char배열을 생성한다. 
		char[] cArr = new char[0];    // char[] cArr = {};와 같다. 
		String s = new String(cArr);  // String s = new String("");와 같다. 

		System.out.println("cArr.length="+cArr.length);
		System.out.println("@@@"+s+"@@@");
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
cArr.length=0
@@@@@@
```

길이가 0인 배열을 생성해서 char형 배열 참조변수 cArr를 초기화 해주었다. 길이가 0이긴 해도 배열이 생성되며, 생성된 배열의 주솟값이 참조변수 cArr에 저장된다.
