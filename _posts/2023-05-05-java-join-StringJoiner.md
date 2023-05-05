---
layout: post
title: join()과 StringJoiner
description: Java의 정석 기초편 ch. 9 java.lang패키지와 유용한 클래스
categories: Java
date: 2023-05-05 14:59:00 +09:00
---
join()은 여러 문자열 사이에 구분자를 넣어서 구분한다. 구분자로 문자열을 자르는 split()과 반대의 작업을 한다고 생각하면 이해하기 쉽다.

```java
String animals = "dog,cat,bear";
String[] arr = animals.split(",");    // 문자열을 ','를 구분자로 나눠서 배열에 저장
String str = String.join("-", arr);   // 배열의 문자열을 '-'로 구분해서 결합
System.out.println(str);              // dog-cat-bear
```

java.util.StringJoiner클래스를 사용해서 문자열을 결합할 수도 있는데, 사용하는 방법은 간단하다. 아래의 코드를 보는 것만으로도 이해가 될 것이다.

```java
StringJoiner sj = new StringJoiner(",", "[", "]");
String[] strArr = { "aaa", "bbb", "ccc" };

for (String s : strArr)
  sj.add(s.toUpperCase());
  
System.out.println(sj.toString());    // [AAA,BBB,CCC]
```

(join()과 java.util.StringJoiner는 JDK1.8부터 추가되었다.)

```java
import java.util.StringJoiner;

class Ex9_9 {
	public static void main(String[] args) {
		String animals = "dog,cat,bear";
		String[] arr   = animals.split(",");

		System.out.println(String.join("-", arr));

		StringJoiner sj = new StringJoiner("/","[","]");

		for(String s : arr)
			sj.add(s);

		System.out.println(sj.toString());
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
dog-cat-bear
[dog/cat/bear]
```
