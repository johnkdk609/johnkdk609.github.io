---
layout: post
title: FileInputStream과 FileOutputStream
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-07 17:54:00 +0900
---
FileInputStream/FileOutputStream은 파일에 입출력을 하기 위한 스트림이다. 실제 프로그래밍에서 많이 사용되는 스트림 중의 하나이다.

<table>
    <tr>
        <th>생성자</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>FileInputStream(String name)</td>
        <td>지정된 파일이름(name)을 가진 실제 파일과 연결된 FileInputStream을 생성한다.</td>
    </tr>
    <tr>
        <td>FileInputStream(File file)</td>
        <td>파일의 이름이 String이 아닌 File인스턴스로 지정해주어야 하는 점을 제외하고 FileInputStream(String name)와 같다.</td>
    </tr>
    <tr>
        <td>FileInputStream(FileDescriptor fdObj)</td>
        <td>파일 디스크립터(fdObj)로 FileInputStream을 생성한다.</td>
    </tr>
</table>

<table>
    <tr>
        <th>생성자</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>FileOutputStream(String name)</td>
        <td>지정된 파일이름(name)을 가진 실제 파일과 연결된 FileOutputStream을 생성한다.</td>
    </tr>
    <tr>
        <td>FileOutputStream(String name, boolean append)</td>
        <td>지정된 파일이름(name)을 가진 실제 파일과 연결된 FileOutputStream을 생성한다. 두 번째 인자인 append를 true로 하면, 출력 시 기존의 파일 내용의 마지막에 덧붙인다. false면, 기존의 파일내용을 덮어쓰게 된다.</td>
    </tr>
    <tr>
        <td>FileOutputStream(File file)</td>
        <td>파일의 이름을 String이 아닌 File인스턴스로 지정해주어야 하는 점을 제외하고 FileOutputStream(String name)과 같다.</td>
    </tr>
    <tr>
        <td>FileOutputStream(File file, boolean append)</td>
        <td>파일의 이름을 String이 아닌 File인스턴스로 지정해주어야 하는 점을 제외하고 FileOutputStream(String name, boolean append)과 같다.</td>
    </tr>
    <tr>
        <td>FileOutputStream(FileDescriptor fdObj)</td>
        <td>파일 디스크립터(fdObj)로 FileOutputStream을 생성한다.</td>
    </tr>
</table>


## FileInputStream과 FileOutputStream 예제 1

```java
import java.io.*;

class FileViewer {
	public static void main(String args[]) throws IOException {
		FileInputStream fis = new FileInputStream(args[0]);
//	 	이클립스에서는 윗 줄 대신 아래 줄 입력하고 Run(ctrl + F11)으로 실행 
//		FileInputStream fis = new FileInputStream(".\\src\\FileViewer.java");

		int data = 0;

		while((data=fis.read())!=-1) {
			char c = (char)data;
			System.out.print(c);
		}
	} 
}
```

커맨드라인으로부터 입력받은 파일의 내용을 읽어서 그대로 화면에 출력하는 간단한 예제이다. read()는 반환값이 int형(4 byte)이긴 하지만, 더 이상 입력값이 없음을 알리는 -1을 제외하고는 0~255(1 byte) 범위의 정수값이기 때문에, char형(2 byte)으로 변환한다 해도 손실되는 값은 없다.

read()가 한 번에 1 byte씩 파일로부터 데이터를 읽어들이긴 하지만, 데이터의 범위가 십진수로 0~255(16진수로 0x00~0xff) 범위의 정수값이고, 또 읽을 수 있는 입력값이 더 이상 없음을 알릴 수 있는 값(-1)도 필요하다. 그래서 다소 크긴 하지만 정수형 중에서는 연산이 가장 효율적이고 빠른 int형 값을 반환하도록 한 것이다.


## FileInputStream과 FileOutputStream 예제 2

```java
import java.io.*;

class FileCopy {
	public static void main(String args[]) {
		try {
			FileInputStream  fis = new FileInputStream(args[0]);
			FileOutputStream fos = new FileOutputStream(args[1]);

			int data =0;
			while((data=fis.read())!=-1)
				fos.write(data);	 // void write(int b)

			fis.close();
			fos.close();
		} catch (IOException e) {
			e.printStackTrace();		
		}
	}
}
```

FileInputStream과 FileOutputStream을 사용해서 FileCopy.java파일의 내용을 그대로 FileCopy.bak로 복사하는 일을 한다.

단순히 FileCopy.java의 내용을 read()로 읽어서, write(int b)로 FileCopy.bak에 출력한다. 이처럼 텍스트파일을 다루는 경우에는 FileInputStream/FileOutputStream보다 문자기반의 스트림인 FileReader/FileWriter를 사용하는 것이 더 좋다.