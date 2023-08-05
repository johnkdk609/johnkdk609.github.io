---
layout: post
title: InputStream과 OutputStream
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-05 16:10:00 +0900
---
앞서 얘기한 바와 같이 InputStream과 OutputStream은 모든 바이트 기반 스트림의 조상이며 다음과 같은 메서드가 선언되어 있다.

<table>
    <tr>
        <th>InputStream 메서드명</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>int available()</td>
        <td>스트림으로부터 읽어올 수 있는 데이터의 크기를 반환한다.</td>
    </tr>
    <tr>
        <td>void close()</td>
        <td>스트림을 닫음으로써 사용하고 있던 자원을 반환한다.</td>
    </tr>
    <tr>
        <td>void mark(int readlimit)</td>
        <td>현재 위치를 표시해 놓는다. 후에 reset()에 의해서 표시해 놓은 위치로 다시 돌아갈 수 있다. readlimit은 되돌아갈 수 있는 byte의 수이다.</td>
    </tr>
    <tr>
        <td>boolean markSupported()</td>
        <td>mark()와 reset()을 지원하는지를 알려준다. mark()와 reset() 기능을 지원하는 것은 선택적이므로, mark()와 reset()을 사용하기 전에 markSupported()를 호출해서 지원 여부를 확인해야 한다.</td>
    </tr>
    <tr>
        <td>abstract int read()</td>
        <td>1 byte를 읽어 온다(0~255 사이의 값). 더 이상 읽어올 데이터가 없으면 -1을 반환한다. abstract메서드라서 InputStream의 자손들은 자신의 상황에 알맞게 구현해야 한다.</td>
    </tr>
    <tr>
        <td>int read(byte[] b)</td>
        <td>배열 b의 크기만큼 읽어서 배열을 채우고 읽어 온 데이터의 수를 반환한다. 반환하는 값은 항상 배열의 크기보다 작거나 같다.</td>
    </tr>
    <tr>
        <td>int read(byte[] b, int off, int len)</td>
        <td>최대 len개의 byte를 읽어서, 배열 b의 지정된 위치(off)부터 저장한다. 실제로 읽어올 수 있는 데이터가 len개보다 적을 수 있다.</td>
    </tr>
    <tr>
        <td>void reset()</td>
        <td>스트림에서의 위치를 마지막으로 mark()이 호출되었던 위치로 되돌린다.</td>
    </tr>
    <tr>
        <td>long skip(long n)</td>
        <td>스트림에서 주어진 길이(n)만큼을 건너뛴다.</td>
    </tr>
</table>

<table>
    <tr>
        <th>OutputStream 메서드명</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>void close()</td>
        <td>입력소스를 닫음으로써 사용하고 있던 자원을 반환한다.</td>
    </tr>
    <tr>
        <td>void flush()</td>
        <td>스트림의 버퍼에 있는 모든 내용을 출력소스에 쓴다.</td>
    </tr>
    <tr>
        <td>abstract void write(int b)</td>
        <td>주어진 값을 출력소스에 쓴다.</td>
    </tr>
    <tr>
        <td>void write(byte[] b)</td>
        <td>주어진 배열 b에 저장된 모든 내용을 출력소스에 쓴다.</td>
    </tr>
    <tr>
        <td>void write(byte[] b, int off, int len)</td>
        <td>주어진 배열 b에 저장된 내용 중에서 off번째부터 len개 만큼만을 읽어서 출력소스에 쓴다.</td>
    </tr>
</table>

스트림의 종류에 따라서 mark()과 reset()을 사용하여 이미 읽은 데이터를 되돌려서 다시 읽을 수 있다. 이 기능을 지원하는 스트림인지 확인하는 markSupported()를 통해서 알 수 있다.

flush()는 버퍼가 있는 출력스트림의 경우에만 의미가 있으며, OutputStream에 정의된 flush()는 아무런 일도 하지 않는다.

프로그램이 종료될 때, 사용하고 닫지 않은 스트림을 JVM이 자동적으로 닫아 주기는 하지만, 스트림을 사용해서 모든 작업을 마치고 난 후에는 close()를 호출해서 반드시 닫아 주어야 한다. 그러나 ByteArrayInputStream과 같이 메모리를 사용하는 스트림과 System.in, System.out과 같은 표준 입출력 스트림은 닫아 주지 않아도 된다.


## InputStream과 OutputStream 예제 1

ByteArrayInputStream/ByteArrayOutputStream은 메모리, 즉 바이트 배열에 데이터를 입출력 하는 데 사용되는 스트림이다. 주로 다른 곳에 입출력하기 전에 데이터를 임시로 바이트 배열에 담아서 변환 등의 작업을 하는 데 사용된다.

자주 사용되지 않지만 스트림을 이용한 입출력 방법을 보여주는 예제를 작성하기에는 적합해서, 이 스트림을 이용해서 읽고 쓰는 여러 방법을 보여주는 예제들을 작성해 보았다.

스트림의 종류가 달라도 읽고 쓰는 방법은 동일하므로 이 예제들을 통해서 스트림에 읽고 쓰는 방법을 잘 익혀두면 좋다.

```java
import java.io.*;
import java.util.Arrays;

class Ex15_1 {
	public static void main(String[] args) {
		byte[] inSrc = {0,1,2,3,4,5,6,7,8,9};
		byte[] outSrc = null;

		ByteArrayInputStream input = null;
		ByteArrayOutputStream output = null;

		input = new ByteArrayInputStream(inSrc);
		output = new ByteArrayOutputStream();

		int data = 0;

		while((data = input.read())!=-1)
			output.write(data);	// void write(int b)

		outSrc = output.toByteArray(); // 스트림의 내용을 byte배열로 반환한다.

		System.out.println("Input Source  :" + Arrays.toString(inSrc));
		System.out.println("Output Source :" + Arrays.toString(outSrc));
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Input Source  :[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
Output Source :[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

ByteArrayInputStream/ByteArrayOutputStream을 이용해서 바이트배열 inSrc의 데이터를 outSrc로 복사하는 예제인데, read()와 write()를 사용하는 가장 기본적인 방법을 보여준다. while문의 조건식이 조금 복잡한데, 이 조건식은 아래와 같은 순서로 처리된다.

```
(data = input.read()) != -1

① data = input.read()   // read()를 호출한 반환값을 변수 data에 저장한다. (괄호 먼저)
② data != -1            // data에 저장된 값이 -1이 아닌지 비교한다.
```

바이트배열은 사용하는 자원이 메모리밖에 없으므로 가비지컬렉터에 의해 자동적으로 자원을 반환하므로 close()로 스트림을 닫지 않아도 된다. read()와 write(int b)를 사용하기 때문에 한 번에 1 byte만 읽고 쓰므로 작업효율이 떨어진다.


## InputStream과 OutputStream 예제 2

```java
import java.io.*;
import java.util.Arrays;

class Ex15_2 {
	public static void main(String[] args) {
		byte[] inSrc = {0,1,2,3,4,5,6,7,8,9};
		byte[] outSrc = null;
		byte[] temp = new byte[10];

		ByteArrayInputStream  input  = null;
		ByteArrayOutputStream output = null;

		input  = new ByteArrayInputStream(inSrc);
		output = new ByteArrayOutputStream();

		input.read(temp,0,temp.length); // 읽어 온 데이터를 배열 temp에 담는다.
		output.write(temp,5, 5);        // temp[5]부터 5개의 데이터를 write한다.

		outSrc = output.toByteArray();

		System.out.println("Input Source  :" + Arrays.toString(inSrc));
		System.out.println("temp          :" + Arrays.toString(temp));
		System.out.println("Output Source :" + Arrays.toString(outSrc));
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Input Source  :[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
temp          :[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
Output Source :[5, 6, 7, 8, 9]
```

int read(byte[] b, int off, int len)와 void write(byte[] b, int off, int len)를 이용해서 입출력하는 방법을 보여주는 예제이다. 이전 예제와는 달리 byte배열을 이용해서 한 번에 배열의 크기만큼 읽고 쓸 수 있다. 바구니(배열 temp)를 이용하면 한 번에 더 많은 물건을 옮길 수 있는 것과 같다고 이해하면 좋을 것이다.

byte배열 temp의 크기(temp.length)가 10이라서 10 byte를 읽어왔지만 output에 출력할 때는 temp[5]부터 5byte만 출력하였다.

```java
input.read(temp, 0, temp.length);   // 읽어온 데이터를 배열 temp에 담는다.
output.write(temp, 5, 5);           // temp[5]부터 5개의 데이터를 write한다.
```

배열을 이용한 입출력은 작업의 효율을 증가시키므로 가능하면 입출력 대상에 따라 알맞은 크기의 배열을 사용하는 것이 좋다.


## InputStream과 OutputStream 예제 3

```java
import java.io.*;
import java.util.Arrays;

class Ex15_3 {
	public static void main(String[] args) {
		byte[] inSrc = {0,1,2,3,4,5,6,7,8,9};
		byte[] outSrc = null;
		byte[] temp = new byte[4];	// 이전 예제와 배열의 크기가 다르다. 

		ByteArrayInputStream  input  = null;
		ByteArrayOutputStream output = null;

		input  = new ByteArrayInputStream(inSrc);
		output = new ByteArrayOutputStream();

		System.out.println("Input Source  :" + Arrays.toString(inSrc));

		try {
			while(input.available() > 0) {
				input.read(temp);
				output.write(temp); 

				outSrc = output.toByteArray();
				printArrays(temp, outSrc);
			}
		} catch(IOException e) {}
	} // main의 끝 

	static void printArrays(byte[] temp, byte[] outSrc) {
		System.out.println("temp          :" +Arrays.toString(temp));
		System.out.println("Output Source :" +Arrays.toString(outSrc));	
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Input Source  :[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
temp          :[0, 1, 2, 3]
Output Source :[0, 1, 2, 3]
temp          :[4, 5, 6, 7]
Output Source :[0, 1, 2, 3, 4, 5, 6, 7]
temp          :[8, 9, 6, 7]
Output Source :[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 6, 7]
```

read()나 write()이 IOException을 발생시킬 수 있기 때문에 try-catch문으로 감싸주었다. 그리고 available()은 블락킹(blocking) 없이 읽어올 수 있는 바이트의 수를 반환한다.

아마도 예상과 다른 결과가 나왔을 텐데 그 이유는 마지막에 읽은 배열의 9번째와 10번째 요소값인 8과 9만을 출력해야 하는데 temp에 남아 있던 6, 7까지 출력했기 때문이다.

temp에 담긴 내용을 지우고 쓰는 것이 아니라 보다 나은 성능을 위해서 그냥 기존의 내용 위에 덮어 쓴다. 그래서 temp의 내용은 '[4, 5, 6, 7]'이었는데, 8과 9를 읽고 난 후에는 '[8, 9, 6, 7]'이 된다.

원하는 결과를 얻기 위해서는 아래 보기 1 코드를 보기 2 코드와 같이 수정해야 한다. 보기 1 코드는 배열의 내용 전체를 출력하지만, 보기 2의 코드는 읽어온 만큼(len)만 출력한다.

```java
// 보기 1

while (input.available() > 0) {
    input.read(temp);
    output.write(temp);
}
```

↓

```java
while (intput.available() > 0) {
    int len = input.read(temp);
    output.write(temp, 0, len);
}
```

(블락킹이란 데이터를 읽어 올 때 데이터를 기다리기 위해 멈춰있는 것을 뜻한다. 예를 들어 사용자가 데이터를 입력하기 전까지 기다리고 있을 때 블락킹 상태에 있다고 한다.)