---
layout: post
title: InputStreamReader, OutputStreamWriter
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-22 11:22:00 +0900
---
InputStreamReader/OutputStreamWriter는 이름에서 알 수 있는 것과 같이 바이트기반 스트림을 문자기반 스트림으로 연결시켜주는 역할을 한다. 그리고 바이트기반 스트림의 데이터를 지정된 인코딩의 문자데이터로 변환하는 작업을 수행한다.

<table>
    <tr>
        <th>생성자 / 메서드</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>InputStreamReader(InputStream in)</td>
        <td>OS에서 사용하는 기본 인코딩의 문자로 변환하는 InputStreamReader를 생성한다.</td>
    </tr>
    <tr>
        <td>InputStreamReader(InputStream in, String encoding)</td>
        <td>지정된 인코딩을 사용하는 InputStreamReader를 생성한다.</td>
    </tr>
    <tr>
        <td>String getEncoding()</td>
        <td>InputStreamReader의 인코딩을 알려준다.</td>
    </tr>
</table>

<table>
    <tr>
        <th>생성자 / 메서드</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>OutputStreamWriter(OutputStream in)</td>
        <td>OS에서 사용하는 기본 인코딩의 문자로 변환하는 OutputStreamWriter를 생성한다.</td>
    </tr>
    <tr>
        <td>OutputStreamWriter(OutputStream in, String encoding)</td>
        <td>지정된 인코딩을 사용하는 OutputStreamWriter를 생성한다.</td>
    </tr>
    <tr>
        <td>String getEncoding()</td>
        <td>OutputStreamWriter의 인코딩을 알려준다.</td>
    </tr>
</table>

한글 윈도우에서 중국어로 작성된 파일을 읽을 때 InputStreamReader(InputStream in, String encoding)를 이용해서 인코딩이 중국어로 되어 있다는 것을 지정해주어야 파일의 내용이 깨지지 않고 올바르게 보일 것이다. 인코딩을 지정해 주지 않는다면 OS에서 사용하는 인코딩을 사용해서 파일을 해석해서 보여주기 때문에 우너래 작성된 대로 볼 수 없을 것이다.

이와 마찬가지로 OutputStreamWriter를 이용해서 파일에 텍스트 데이터를 저장할 때 생성자 OutputStreamWriter(OutputStream in, String ecoding)를 이용해서 인코딩을 지정하지 않으면 OS에서 사용하는 인코딩으로 데이터를 저장할 것이다.

```java
Properties prop = System.getProperties();
System.out.println(prop.get("sun.jnu.encoding"));
```

```java
import java.io.*;

class Ex15_12 {
	public static void main(String[] args) {
		String line = "";

		try {
			InputStreamReader isr = new InputStreamReader(System.in);
			BufferedReader br = new BufferedReader(isr);

			System.out.println("사용중인 OS의 인코딩 :" + isr.getEncoding());

			do {
				System.out.print("문장을 입력하세요. 마치시려면 q를 입력하세요.>");
				line = br.readLine();
				System.out.println("입력하신 문장 : "+line);
			} while(!line.equalsIgnoreCase("q"));

//			br.close();   // System.in과 같은 표준입출력은 닫지 않아도 된다. 
			System.out.println("프로그램을 종료합니다.");
		} catch(IOException e) {}
	} // main
}
```

위 코드의 출력 결과 예시는 다음과 같다.

```
사용중인 OS의 인코딩 :UTF8
문장을 입력하세요. 마치시려면 q를 입력하세요.>asdf
입력하신 문장 : asdf
문장을 입력하세요. 마치시려면 q를 입력하세요.>hello
입력하신 문장 : hello
문장을 입력하세요. 마치시려면 q를 입력하세요.>q
입력하신 문장 : q
프로그램을 종료합니다.
```

BufferedReader의 readLine()을 이용해서 사용자의 화면입력을 라인 단위로 입력받으면 편리하다. 그리고 BufferedReader와 InputStream인 System.in을 연결하기 위해 InputStreamReader를 사용하였다. JDK1.5부터는 Scanner가 추가되어 이와 같은 방식을 사용하지 않아도 간단하게 처리가 가능하다. 

그리고 현재 사용 중인 OS의 인코딩을 확인하려면 생성자 InputStreamReader(InputStream in)를 사용해서 InputStreamReader의 인스턴스를 생성한 다음, getEncoding()을 호출하면 된다.

한글 Windows에서 사용하는 인코딩의 종류는 MS949이며, 이 예제를 실행하는 OS의 종류에 따라 인코딩이 다를 수 있다.