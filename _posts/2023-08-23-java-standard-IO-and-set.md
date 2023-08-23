---
layout: post
title: 표준 입출력(Standard I/O)과 대상변경
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-23 17:36:00 +0900
---
표준 입출력은 콘솔(console, 도스창)을 통한 데이터 입력과 콘솔로의 데이터 출력을 의미한다. 자바에서는 표준 입출력(standard I/O)을 위해 3가지 입출력 스트림, System.in, System.out, System.err을 제공하는데, 이들은 자바 어플리케이션의 실행과 동시에 사용할 수 있게 자동적으로 생성되기 때문에 개발자가 별도로 스트림을 생성하는 코드를 작성하지 않고도 사용이 가능하다.

자바를 처음 시작할 때부터 지금까지 줄곧 사용해온 System.out을 스트림의 생성 없이 사용할 수 있었던 것은 바로 이러한 이유 때문이다.

> **System.in** - 콘솔로부터 데이터를 입력받는 데 사용 (표준 출력)\
> **System.out** - 콘솔로 데이터를 출력하는 데 사용 (표준 입력)\
> **System.err** - 콘솔로 데이터를 출력하는 데 사용 (표준 입력)

System클래스의 소스에서 알 수 있듯이 in, out, err은 System클래스에 선언된 클래스변수(static변수)이다. 선언부분만을 봐서는 out, err, in의 타입은 InputStream과 PrintStream이지만 실제로는 버퍼를 이용하는 BufferedInputStream과 BufferedOutputStream의 인스턴스를 사용한다.

```java
public final class System {
    public final static InputStream in = nullInputStream();
    public final static PrintStream out = nullPrintStream();
    public final static PrintStream err = nullPrintStream();
        ...
}
```


## 표준 입출력의 대상변경

초기에는 System.in, System.out, System.err의 입출력대상이 콘솔 화면이지만, setIn(), setOut(), setErr()를 사용하면 입출력을 콘솔 이외에 다른 입출력 대상으로 변경하는 것이 가능하다.

<table>
    <tr>
        <th>메서드</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>static void setOut(PrintStream out)</td>
        <td>System.out의 출력을 지정된 PrintStream으로 변경</td>
    </tr>
    <tr>
        <td>static void setErr(PrintStream err)</td>
        <td>System.err의 출력을 지정한 PrintStream으로 변경</td>
    </tr>
    <tr>
        <td>static void setIn(InputStream in)</td>
        <td>System.in의 입력을 지정한 InputStream으로 변경</td>
    </tr>
</table>

그러나 JDK1.5부터 Scanner클래스가 제공되면서 System.in으로부터 데이터를 입력받아 작업하는 것이 편리해졌다.

```java
class Ex15_13 {
	public static void main(String[] args) {
		System.out.println("out : Hello World!");
		System.err.println("err : Hello World!");
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
out : Hello World!
err : Hello World!
```

System.out, System.err 모두 출력대상이 콘솔이기 때문에 System.out대신 System.err을 사용해도 같은 결과를 얻는다.


## 표준 입출력의 대상변경 예제

```java
import java.io.*;

class Ex15_14 {
	public static void main(String[] args) {
		PrintStream ps = null;
		FileOutputStream fos=null;

		try {
			fos = new FileOutputStream("test.txt");
			ps = new PrintStream(fos);
			System.setOut(ps);    //  System.out의 출력대상을 test.txt파일로 변경 
		} catch(FileNotFoundException e) {
			System.err.println("File not found.");
		}

		System.out.println("Hello by System.out");		
		System.err.println("Hello by System.err");		
	}
}
```

System.out의 출력소스를 test.txt파일로 변경하였기 때문에 System.out을 이용한 출력은 모두 test.txt파일에 저장된다. 그래서 실행결과에는 System.err를 이용한 출력만 나타난다.