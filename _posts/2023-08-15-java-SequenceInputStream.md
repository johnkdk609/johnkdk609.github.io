---
layout: post
title: SequenceInputStream
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-15 13:06:00 +0900
---
SequenceInputStream은 여러 개의 입력스트림을 연속적으로 연결해서 하나의 스트림으로부터 데이터를 읽는 것과 같이 처리할 수 있도록 도와준다. SequenceInputStream의 생성자를 제외하고 나머지 작업은 다른 입력스트림과 다르지 않다. 큰 파일을 여러 개의 작은 파일로 나누었다가 하나의 파일로 합치는 것과 같은 작업을 수행할 때 사용하면 좋을 것이다.

(SequenceInputStream은 다른 보조 스트림들과는 달리 FilterInputStream의 자손이 아닌 InputStream을 바로 상속받아서 구현하였다.)

<table>
    <tr>
        <th>생성자</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>SequenceInputStream(Enumeration e)</td>
        <td>Enumeration에 저장된 순서대로 입력스트림을 하나의 스트림으로 연결한다.</td>
    </tr>
    <tr>
        <td>SequenceInputStream(InputStream s1, InputStream s2)</td>
        <td>두 개의 입력스트림 s1, s2를 하나로 연결한다.</td>
    </tr>
</table>

위 생성자들을 사용하는 방법은 다음과 같다.

[사용 예 1]

```java
Vector files = new Vector();
files.add(new FileInputStream("file.001"));
files.add(new FileInputStream("file.002"));
SequenceInputStream in = new SequenceInputStream(files.elements());
```

[사용 예 2]

```java
FileInputStream file1 = new FileInputStream("file.001");
FileInputStream file2 = new FileInputStream("file.002");
SequenceInputStream in = new SequenceInputStream(file1, file2)
```


## SequenceInputStream 예제

```java
import java.io.*;
import java.util.*;

class Ex15_7 {
	public static void main(String[] args) {
		byte[] arr1 = {0,1,2};
		byte[] arr2 = {3,4,5};
		byte[] arr3 = {6,7,8};
		byte[] outSrc = null;

		Vector v = new Vector();
		v.add(new ByteArrayInputStream(arr1));
		v.add(new ByteArrayInputStream(arr2));
		v.add(new ByteArrayInputStream(arr3));

		SequenceInputStream   input  = new SequenceInputStream(v.elements());
		ByteArrayOutputStream output = new ByteArrayOutputStream();

		int data = 0;

		try {
			while((data = input.read())!=-1) {
				output.write(data);	// void write(int b)
			}
		} catch(IOException e) {}

		outSrc = output.toByteArray();

		System.out.println("Input Source1  :" + Arrays.toString(arr1));
		System.out.println("Input Source2  :" + Arrays.toString(arr2));
		System.out.println("Input Source3  :" + Arrays.toString(arr3));
		System.out.println("Output Source  :" + Arrays.toString(outSrc));
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Input Source1  :[0, 1, 2]
Input Source2  :[3, 4, 5]
Input Source3  :[6, 7, 8]
Output Source  :[0, 1, 2, 3, 4, 5, 6, 7, 8]
```

3개의 ByteArrayInputStream을 Vector와 SequenceInputStream을 이용해서 하나의 입력스트림처럼 다룰 수 있다. Vector에 저장된 순서대로 입력되므로 순서에 주의해야 한다.