---
layout: post
title: File
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-24 21:04:00 +0900
---
파일은 기본적이면서도 가장 많이 사용되는 입출력 대상이기 때문에 중요하다.

자바에서는 File클래스를 통해서 파일과 디렉토리를 다룰 수 있도록 하고 있다. 그래서 File인스턴스는 파일일 수도 있고 디렉토리일 수도 있다.

먼저 File의 생성자와 경로에 관련된 메서드를 알아보자.

<table>
    <tr>
        <th>생성자 / 메서드</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>File(String fileName)</td>
        <td>주어진 문자열(fileName)을 이름으로 갖는 파일을 위한 File인스턴스를 생성한다. 파일뿐만 아니라 디렉토리도 같은 방법으로 다룬다.<br>여기서 fileName은 주로 경로(path)를 포함해서 지정해주지만, 파일 이름만 사용해도 되는데 이 경우 프로그램이 실행되는 위치가 경로(path)로 간주된다.</td>
    </tr>
    <tr>
        <td>File(String pathName, String fileName)<br>File(File pathName, String fileName)</td>
        <td>파일의 경로와 이름을 따로 분리해서 지정할 수 있게 한 생성자. 이 중 두 번째 것은 경로를 문자열이 아닌 File인스턴스인 경우를 위해서 제공된 것이다.</td>
    </tr>
    <tr>
        <td>File(URI uri)</td>
        <td>지정된 uri로 파일을 생성</td>
    </tr>
    <tr>
        <td>String getName()</td>
        <td>파일이름을 String으로 반환</td>
    </tr>
    <tr>
        <td>String getPath()</td>
        <td>파일의 경로(path)를 String으로 반환</td>
    </tr>
    <tr>
        <td>String getAbsolutePath()<br>File getAbsoluteFile()</td>
        <td>파일의 절대경로를 String으로 반환<br>파일의 절대경로를 File로 반환</td>
    </tr>
    <tr>
        <td>String getParent()<br>File getParentFile()</td>
        <td>파일의 조상 디렉토리를 String으로 반환<br>파일의 조상 디렉토리를 File로 반환</td>
    </tr>
    <tr>
        <td>String getCanonicalPath()<br>File getCanonicalFile()</td>
        <td>파일의 정규경로를 String으로 반환<br>파일의 정규경로를 File로 반환</td>
    </tr>
</table>

<table>
    <tr>
        <th>멤버변수</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>static String pathSeparator</td>
        <td>OS에서 사용하는 경로(path) 구분자. 윈도우 ";", 유닉스 ":"</td>
    </tr>
    <tr>
        <td>static char pathSeparatorChar</td>
        <td>OS에서 사용하는 경로(path) 구분자.<br>윈도우에서는 ';', 유닉스 ':'</td>
    </tr>
    <tr>
        <td>static String separator</td>
        <td>OS에서 사용하는 이름 구분자. 윈도우 "￦", 유닉스 "/"</td>
    </tr>
    <tr>
        <td>static char separatorChar</td>
        <td>OS에서 사용하는 이름 구분자. 윈도우 '￦', 유닉스 '/'</td>
    </tr>
</table>


## File 예제 1

```java
import java.io.*;

class Ex15_15 {
	public static void main(String[] args) throws IOException {
		File f = new File("c:\\jdk1.8\\work\\ch15\\Ex15_15.java");
		String fileName = f.getName();
		int pos = fileName.lastIndexOf(".");

		System.out.println("경로를 제외한 파일이름 - " + f.getName());
		System.out.println("확장자를 제외한 파일이름 - "+ fileName.substring(0,pos));
		System.out.println("확장자 - " + fileName.substring(pos+1));

		System.out.println("경로를 포함한 파일이름 	- " + f.getPath());
		System.out.println("파일의 절대경로         - " + f.getAbsolutePath());
		System.out.println("파일의 정규경로         - " + f.getCanonicalPath());
		System.out.println("파일이 속해 있는 디렉토리	- " + f.getParent());
		System.out.println();
		System.out.println("File.pathSeparator - " + File.pathSeparator);
		System.out.println("File.pathSeparatorChar - " + File.pathSeparatorChar);
		System.out.println("File.separator - " + File.separator);
		System.out.println("File.separatorChar - " + File.separatorChar);
		System.out.println();
		System.out.println("user.dir="+System.getProperty("user.dir"));
		System.out.println("sun.boot.class.path=" + System.getProperty("sun.boot.class.path"));
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
경로를 제외한 파일이름 - c:\jdk1.8\work\ch15\Ex15_15.java
확장자를 제외한 파일이름 - c:\jdk1.8\work\ch15\Ex15_15
확장자 - java
경로를 포함한 파일이름 	- c:\jdk1.8\work\ch15\Ex15_15.java
파일의 절대경로         - /Users/kdk/eclipse-workspace/ch15/c:\jdk1.8\work\ch15\Ex15_15.java
파일의 정규경로         - /Users/kdk/eclipse-workspace/ch15/c:\jdk1.8\work\ch15\Ex15_15.java
파일이 속해 있는 디렉토리	- null

File.pathSeparator - :
File.pathSeparatorChar - :
File.separator - /
File.separatorChar - /

user.dir=/Users/kdk/eclipse-workspace/ch15
sun.boot.class.path=/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib/resources.jar:/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib/rt.jar:/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib/jsse.jar:/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib/jce.jar:/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib/charsets.jar:/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib/jfr.jar:/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/classes
```

File인스턴스를 생성하고 메서드를 이용해서 파일의 경로와 구분자 등의 정보를 출력하는 예제이다. 결과를 보면 어떤 결과를 얻기 위해서는 어떤 메서드를 사용해야 하는지 감이 잡힐 것이다.

절대경로(absolute path)는 파일시스템의 루트(root)로부터 시작하는 파일의 전체 경로를 의미한다. OS에 따라 다르지만, 하나의 파일에 대해 둘 이상의 절대경로가 존재할 수 있다. 현재 디렉토리를 의미하는 '.'와 같은 기호나 링크를 포함하고 있는 경우가 이에 해당한다. 그러나 정규경로(canonical path)는 기호나 링크 등을 포함하지 않는 유일한 경로를 의미한다.

예를 들어 'C:\jdk1.8\work\ch15\Ex15_5.java'의 또 다른 절대경로는 'C:\jdk1.8\work\ch15\.\Ex15_15.java'가 있지만, 정규경로는 'C:\jdk1.8\work\ch15\Ex15_15.java' 단 하나 뿐이다.

시스템속성 중에서 user.dir의 값을 확인하면 현재 프로그램이 실행 중인 디렉토리를 알 수 있다. 그리고 우리가 OS의 시스템변수로 설정하는 classpath 외에 sun.boot.class.path라는 시스템속성에 기본적인 classpath가 있어서 기본적인 경로들은 이미 설정되어 있다. 그래서 처음에 JDK 설치 후 classpath를 따로 지정해주지 않아도 되는 것이다. 이 속성은 JDK1.2 이후부터 추가된 것이라 그 이전의 버전에서는 rt.jar와 같은 파일을 classpath에 지정해주어야 했다.

예제에서 사용된 'File f = new File("c:\\jdk1.8\\work\\ch15\\Ex15_15.java");' 대신 다른 생성자를 사용해서 File인스턴스를 생성할 수 있다.

```java
File f = new File("c:\\jdk1.8\\work\\ch15", "Ex15_15.java");
            또는
File dir = new File("c:\\jdk1.8\\work\\ch15");
File f = new File(dir, "Ex15_15.java");
```

한 가지 더 알아두어야 할 것은 File인스턴스를 생성했다고 해서 파일이나 디렉토리가 생성되는 것은 아니라는 것이다. 파일명이나 디렉토리명으로 지정된 문자열이 유효하지 않더라도 컴파일 에러나 예외를 발생시키지 않는다.

새로운 파일을 생성하기 위해서는 File인스턴스를 생성한 다음, 출력스트림을 생성하거나 createNewFile()을 호출해야 한다.

> 1. 이미 존재하는 파일을 참조할 때 :\
> File f = new File("c:\\jdk1.8\\work\\ch15", "Ex15_15.java");\
>
> 2. 기존에 없는 파일을 새로 생성할 때 :\
> File f = new File("c:\\jdk1.8\\work\\ch15", "NewFile.java");\
> f.createNewFile();    // 새로운 파일이 생성된다.