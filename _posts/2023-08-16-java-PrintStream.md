---
layout: post
title: PrintStream
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-16 11:16:00 +0900
---
PrintStream은 데이터를 기반스트림에 다양한 형태로 출력할 수 있는 print, println, printf와 같은 메서드를 오버로딩하여 제공한다.

PrintStream은 데이터를 적절한 문자로 출력하는 것이기 때문에 문자기반 스트림의 역할을 수행한다. 그래서 JDK1.1에서부터 PrintStream보다 향상된 기능의 문자기반 스트림인 PrintWriter가 추가되었으나 그 동안 매우 빈번히 사용되던 System.out이 PrintStream이다보니 둘 다 사용할 수밖에 없게 되었다.

PrintStream과 PrintWriter는 거의 같은 기능을 가지고 있지만 PrintWriter가 PrintStream에 비해 다양한 언어의 문자를 처리하는 데 적합하기 때문에 가능하면 PrintWriter를 사용하는 것이 좋다.

<table>
    <tr>
        <th colspan="2">생성자 / 메서드</th>
        <th>설명</th>
    </tr>
    <tr>
        <td colspan="2">PrintStream(File file)<br>PrintStream(File file, String csn)<br>PrintStream(OutputStream out)<br>PrintStream(OutputStream out, boolean autoFlush)<br>PrintStream(OutputStream out, boolean autoFlush, String encoding)<br>PrintStream(String fileName)<br>PrintStream(String fileName, String csn)</td>
        <td>지정된 출력스트림을 기반으로 하는 PrintStream인스턴스를 생성한다. autoFlush의 값을 true로 하면 println메서드가 호출되거나 개행문자가 출력될 때 자동으로 flush된다. 기본값은 false이다.</td>
    </tr>
    <tr>
        <td colspan="2">boolean checkError()</td>
        <td>스트림을 flush하고 에러가 발생했는지를 알려 준다.</td>
    </tr>
    <tr>
        <td>void print(boolean b)<br>void print(char c)<br>void print(char[] c)<br>void print(double d)<br>void print(float f)<br>void print(int i)<br>void print(long l)<br>void print(Object o)<br>void print(String s)</td>
        <td>void println(boolean b)<br>void println(char c)<br>void println(char[] c)<br>void println(double d)<br>void println(float f)<br>void println(int i)<br>void println(long l)<br>void println(Object o)<br>void println(String s)</td>
        <td>인자로 주어진 값을 출력소스에 문자로 출력한다.<br>println메서드는 출력 후 줄바꿈을 하고, print메서드는 줄을 바꾸지 않는다.</td>
    </tr>
    <tr>
        <td colspan="2">void println()</td>
        <td>줄바꿈 문자(line separator)를 출력함으로써 줄을 바꾼다.</td>
    </tr>
    <tr>
        <td colspan="2">PrintStream printf(String format, Object... args)</td>
        <td>정형화된(formatted) 출력을 가능하게 한다.</td>
    </tr>
    <tr>
        <td colspan="2">protected void setError()</td>
        <td>작업 중에 오류가 발생했음을 알린다.<br>(setError()를 호출한 후에, checkError()를 호출하면 true를 반환한다.)</td>
    </tr>
</table>