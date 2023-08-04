---
layout: post
title: 바이트 기반 스트림과 문자 기반 스트림의 비교
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-04 15:33:00 +0900
---
아래의 표는 바이트기반 스트림과 문자기반 스트림의 읽기와 쓰기에 사용되는 메서드를 비교한 것인데 byte배열 대신 char배열을 사용한다는 것과 추상메서드가 달라졌다. Reader와 Writer에서도 역시 추상메서드가 아닌 메서드들은 추상메서드를 이용해서 작성되었으며, 프로그래밍적인 관점에서 볼 때 read()를 추상메서드로 하는 것보다 ```int read(char[] cbuf, int off, int len)```를 구상메서드로 하는 것이 더 바람직하다.

바이트기반 스트림과 문자기반 스트림은 이름만 조금 다를 뿐 활용 방법은 거의 같다.

<table>
    <tr>
        <th>InputStream</th>
        <th>Reader</th>
    </tr>
    <tr>
        <td>abstract int read()<br>int read(byte[] b)<br>int read(byte[] b, int off, int len)</td>
        <td>int read()<br>int read(char[] cbuf)<br>abstract int read(char[] cbuf, int off, int len)</td>
    </tr>
</table>

<table>
    <tr>
        <th>OutputStream</th>
        <th>Writer</th>
    </tr>
    <tr>
        <td>abstract void write(int b)<br>void write(byte[] b)<br>void write(byte[] b, int off, int len)<br><br><br></td>
        <td>void write(int c)<br>void write(char[] cbuf)<br>abstract void write(char[] cbuf, int off, int len)<br>void write(String str)<br>void write(String str, int off, int len)</td>
    </tr>
</table>

보조 스트림 역시 다음과 같은 문자기반 보조 스트림이 존재하며 사용 목적과 방식은 바이트기반 보조 스트림과 다르지 않다.

<table>
    <tr>
        <th>바이트기반 보조스트림</th>
        <th>문자기반 보조스트림</th>
    </tr>
    <tr>
        <td>BufferedInputStream<br>BufferedOutputStream</td>
        <td>BufferedReader<br>BufferedWriter</td>
    </tr>
    <tr>
        <td>FilterInputStream<br>FilterOutputStream</td>
        <td>FilterReader<br>FilterWriter</td>
    </tr>
    <tr>
        <td>LineNumberInputStream (deprecated)</td>
        <td>LineNumberReader</td>
    </tr>
    <tr>
        <td>PrintStream</td>
        <td>PrintWriter</td>
    </tr>
    <tr>
        <td>PushbackInputStream</td>
        <td>PushbackReader</td>
    </tr>
</table>