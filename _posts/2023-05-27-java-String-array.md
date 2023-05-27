---
layout: post
title: String배열과 String클래스
description: Java의 정석 기초편 ch. 5 배열
categories: Java
date: 2023-05-27 21:17:00 +09:00
---
배열의 타입이 String인 경우에도 선언과 생성은 int배열의 선언과 생성 방법과 다르지 않다. 예를 들어 3개의 문자열(String)을 담을 수 있는 배열을 생성하는 문장은 다음과 같다.

```java
String[] name = new String[3];    // 3개의 문자열을 담을 수 있는 배열을 생성한다.
```

위의 문장을 수행한 결과를 그림으로 표현하면 다음과 같다. 3개의 String타입의 참조변수를 저장하기 위한 공간이 마련되고 참조형 변수의 기본값은 null이므로 각 요소의 값은 null로 초기화 된다.

(null은 어떠한 객체도 가리키고 있지 않다는 뜻이다.)

<img width="500" alt="[자바의 정석 - 기초편] ch5-12,13 String배열" title="[자바의 정석 - 기초편] ch5-12,13 String배열" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/42fb9ba8-1e2a-4ac4-884a-970df43c7556">

참고로 변수의 타입에 따른 기본값은 다음과 같다.

<table>
  <tr>
    <th>자료형</th>
    <th>기본값</th>
  </tr>
  <tr>
    <td>boolean</td>
    <td>false</td>
  </tr>
  <tr>
    <td>char</td>
    <td>'\u0000'</td>
  </tr>
  <tr>
    <td>byte, short, int</td>
    <td>0</td>
  </tr>
  <tr>
    <td>long</td>
    <td>0L</td>
  </tr>
  <tr>
    <td>float</td>
    <td>0.0f</td>
  </tr>
  <tr>
    <td>double</td>
    <td>0.0d 또는 0.0</td>
  </tr>
  <tr>
    <td>참조형</td>
    <td>null</td>
  </tr>
</table>


## String배열의 초기화

초기화 역시 int배열과 동일한 방법으로 한다. 아래와 같이 배열의 각 요소에 문자열을 지정하면 된다.

```java
String[] name = new String[3];    // 길이가 3인 String배열을 생성
name[0] = "Kim";
name[1] = "Park";
name[2] = "Yi";
```

또는 괄호{}를 사용해서 다음과 같이 간단히 초기화할 수도 있다.

```java
String[] name = new String[]{ "Kim", "Park", "Yi" };
String[] name = { "Kim", "Park", "Yi" };    // new String[]을 생략할 수 있음
```

<img width="500" alt="[자바의 정석 - 기초편] ch5-12,13 String배열" title="[자바의 정석 - 기초편] ch5-12,13 String배열" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/d779e0ca-d15a-49f3-83db-6d1080cbe21f">

특별히 String클래스만 "Kim"과 같이 큰따옴표만으로 간략히 표현하는 것이 허용되지만, 원래 String은 클래스이므로 아래의 보기 1 코드처럼 new연산자를 통해 객체를 생성해야 한다.

```java
// 보기 1

String[] name = new String[3];
name[0] = new String("Kim");
name[1] = new String("Park");
name[2] = new String("Yi");
```

```java
// 보기 2

String[] name = new String[3];
name[0] = "Kim";
name[1] = "Park";
name[2] = "Yi";
```

```java
class Ex5_6 {
	public static void main(String[] args) {
		String[] names = {"Kim", "Park", "Yi"};

		for(int i=0; i < names.length;i++)
			System.out.println("names["+i+"]:"+names[i]);

		String tmp = names[2]; // 배열 names의 세 번째 요소를 tmp에 저장 
		System.out.println("tmp:"+tmp);
		names[0] = "Yu"; // 배열 names의 첫 번째 요소를 "Yu"로 변경 

		for(int i=0; i < names.length;i++)
			System.out.println(names[i]);
	} // main
}
```

위 코드의 출력 결과는 다음과 같다.

```
names[0]:Kim
names[1]:Park
names[2]:Yi
tmp:Yi
Yu
Park
Yi
```


## String클래스

지금까지 여러 문자, 즉 문자열을 저장할 때 String타입의 변수를 사용했다. 사실 문자열이라는 용어는 '문자를 연이어 늘어놓은 것'을 의미하므로 문자배열인 char배열과 같은 뜻이다.

그런데 자바에서는 char배열이 아닌 String클래스를 이용해서 문자열을 처리하는 이유는 String클래스가 char배열에 여러 가지 기능을 추가하여 확장한 것이기 때문이다.

그래서 char배열을 사용하는 것보다 String클래스를 사용하는 것이 문자열을 다루기 더 편리하다.

> **String클래스는 char배열에 기능(메서드)을 추가한 것이다.**

C언어에서는 문자열을 char배열로 다루지만, 객체지향언어인 자바에서는 char배열과 그에 관련된 기능들을 함께 묶어서 클래스에 정의한다. 객체지향개념이 나오기 이전의 언어들은 데이터와 기능을 따로 다루었지만, 객체지향언어에서는 데이터와 그에 관련된 기능을 하나의 클래스에 묶어서 다룰 수 있게 한다. 즉, 서로 관련된 것들끼리 데이터와 기능을 구분하지 않고 함께 묶는 것이다. 

여기서 말하는 '기능'은 함수를 의미하며, 메서드는 객체지향 언어에서 '함수' 대신 사용하는 용어일 뿐 함수와 같은 것이다.

char배열과 String클래스의 한 가지 중요한 차이가 있는데, String객체(문자열)는 읽을 수만 있을 뿐 내용을 변경할 수 없다는 것이다.

```java
String str = "Java";
str = str + "8";            // "Java8"이라는 새로운 문자열이 str에 저장된다.
System.out.println(str);    // "Java8"
```

위의 문장에서 문자열 str의 내용이 변경되는 것 같지만, 문자열은 변경할 수 없으므로 새로운 내용의 문자열이 생성된다.

(변경 가능한 문자열을 다루려면 StringBuffer클래스를 사용하면 된다.)


## String클래스의 주요 메서드

String클래스는 상당히 많은 문자열 관련 메서드들을 제공한다. 아래는 그 중 가장 기본적인 몇 가지이다.

<table>
  <tr>
    <th>메서드</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>char charAt(int index)</td>
    <td>문자열에서 해당 위치(index)에 있는 문자를 반환한다.</td>
  </tr>
  <tr>
    <td>int length()</td>
    <td>문자열의 길이를 반환한다.</td>
  </tr>
  <tr>
    <td>String substring(int from, int to)</td>
    <td>문자열에서 해당 범위(from~to)의 문자열을 반환한다.(to는 포함 안 됨)</td>
  </tr>
  <tr>
    <td>boolean equals(Object obj)</td>
    <td>문자열의 내용이 같은지 확인한다. 같으면 결과는 true, 다르면 false</td>
  </tr>
  <tr>
    <td>char[] toCharArray()</td>
    <td>문자열을 문자배열(char[])로 변환해서 반환한다.</td>
  </tr>
</table>

charAt메서드는 문자열에서 지정된 index에 있는 한 문자를 가져온다. 배열에서 '배열이름\[index\]'로 index에 위치한 값을 가져오는 것과 같다고 생각하면 된다. 배열과 마찬가지로 charAt메서드의 index값은 0부터 시작한다.

```java
String str = "ABCDE";
char ch = str.charAt(3);    // 문자열 str의 4번째 문자 'D'를 ch에 저장
```

<table>
  <tr>
    <th>index</th>
    <td>0</td>
    <td>1</td>
    <td>2</td>
    <td>3</td>
    <td>4</td>
  </tr>
  <tr>
    <th>문자</th>
    <td>A</td>
    <td>B</td>
    <td>C</td>
    <td>D</td>
    <td>E</td>
  </tr>
</table>

substring()은 문자열의 일부를 뽑아낼 수 있다. 주의할 것은 범위의 끝은 포함되지 않는다는 것이다. 예를 들어, index의 범위가 1~4라면 4는 범위에 포함되지 않는다.

```java
String str = "012345";
String tmp = str.substring(1, 4);   // str에서 index 범위 1~4의 문자들을 반환
System.out.println(tmp);            // "123"이 출력된다.
```

equals()는 문자열의 내용이 같은지 다른지 확인하는 데 사용한다. 기본형 변수의 값을 비교하는 경우 '=='연산자를 사용하지만, 문자열의 내용을 비교할 때는 equals()를 사용해야 한다. 그리고 이 메서드는 대소문자를 구분한다는 점에 주의해야 한다. 대소문자를 구분하지 않고 비교하려면 equals() 대신 equalsIgnoreCase()를 사용해야 한다. 

```java
String str = "abc";

if (str.equals("abc")) {    // str과 "abc"의 내용이 같은지 확인한다. 
    ...
}
```
