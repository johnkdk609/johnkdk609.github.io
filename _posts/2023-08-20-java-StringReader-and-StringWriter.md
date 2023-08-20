---
layout: post
title: StringReader와 StringWriter
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-20 23:14:00 +0900
---
StringReader/StringWriter는 CharArrayReader/CharArrayWriter와 같이 입출력 대상이 메모리인 스트림이다. StringWriter에 출력되는 데이터는 내부의 StringBuffer에 저장되며 StringWriter의 다음과 같은 메서드를 이용해서 저장된 데이터를 얻을 수 있다.

> **StringBuffer getBuffer()** - StringWriter에 출력한 데이터가 저장된 StringBuffer를 반환한다.\
> **String toString()** - StringWriter에 출력된 (StringBuffer에 저장된) 문자열을 반환한다.

근본적으로는 String도 char배열이지만, 아무래도 char배열보다는 String으로 처리하는 것이 여러모로 편리한 경우가 더 많을 것이다.

```java
import java.io.*;

class Ex15_10 {
	public static void main(String[] args) {
		String inputData = "ABCD";
		StringReader input  = new StringReader(inputData);
		StringWriter output = new StringWriter();

		int data = 0;

		try {
			while((data = input.read())!=-1) {
				output.write(data);	// void write(int b)
			}
		} catch(IOException e) {}

		System.out.println("Input Data  :" + inputData);
		System.out.println("Output Data :" + output.toString());
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Input Data  :ABCD
Output Data :ABCD
```