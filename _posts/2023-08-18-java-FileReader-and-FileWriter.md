---
layout: post
title: FileReader와 FileWriter
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-18 14:34:00 +0900
---
FileReader/FileWriter는 파일로부터 텍스트 데이터를 읽고, 파일에 쓰는 데 사용된다. 사용 방법은 FileInputStream/FileOutputStream과 다르지 않다.

```java
import java.io.*;

class Ex15_8 {
	public static void main(String args[]) {
		try {
			String fileName = "test.txt";
			FileInputStream fis = new FileInputStream(fileName);
			FileReader fr = new FileReader(fileName);

			int data =0;
			// FileInputStream을 이용해서 파일 내용을 읽어 화면에 출력한다. 
			while((data=fis.read())!=-1) {
				System.out.print((char)data);
			}
			System.out.println();
			fis.close();

			// FileReader를 이용해서 파일 내용을 읽어 화면에 출력한다. 
			while((data=fr.read())!=-1)
				System.out.print((char)data);
			System.out.println();
			fr.close();				
		} catch (IOException e) {
				e.printStackTrace();		
		}
	} // main
}
```

위 코드의 출력 결과는 다음과 같다.

```
Hello by System.out

Hello by System.out
```

이 예제는 바이트기반 스트림인 FileInputStream과 문자기반 스트림인 FileReader의 차이점을 보여주기 위한 것으로 같은 내용의 파일(test.txt)을 한 번은 FileInputStream으로 다른 한 번은 FileReader로 읽어서 화면에 출력했다.


```java
import java.io.*;

class Ex15_9 {
	public static void main(String args[]) {
		try {
			FileReader fr = new FileReader(args[0]);
			FileWriter fw = new FileWriter(args[1]);

			int data =0;
			while((data=fr.read())!=-1) {
			    if(data!='\t' && data!='\n' && data!=' ' && data !='\r')
					  fw.write(data);
			}

			fr.close();
			fw.close();
		} catch (IOException e) {
			e.printStackTrace();		
		}
	} // main
}
```

파일의 공백을 모두 없애는 예제인데 입력스트림으로부터 읽은 데이터를 변환해서 출력스트림에 쓰는 작업의 예를 보여주기 위한 것이다.