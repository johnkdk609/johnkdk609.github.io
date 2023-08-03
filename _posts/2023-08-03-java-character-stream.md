---
layout: post
title: 문자기반 스트림 - Reader, Writer
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-03 10:39:00 +0900
---
지금까지 알아본 스트림은 모두 바이트 기반의 스트림이었다. 바이트기반이라 함은 입출력의 단위가 1 byte라는 뜻이다. 이미 알고 있는 것과 같이 C언어와 달리 Java에서는 한 문자를 의미하는 char형이 1 byte가 아니라 2 byte이기 때문에 바이트기반의 스트림으로 2 byte인 문자를 처리하는 데는 어려움이 있다.

이 점을 보완하기 위해서 문자기반의 스트림이 제공된다. 문자 데이터를 입출력할 때는 바이트기반 스트림 대신 문자기반 스트림을 사용해야 한다.

> InputStream → Reader\
> OutputStream → Writer

<table>
    <tr>
        <th>바이트기반 스트림</th>
        <th>문자기반 스트림</th>
    </tr>
    <tr>
        <td>FileInputStream<br>FileOutputStream</td>
        <td>FileReader<br>FileWriter</td>
    </tr>
    <tr>
        <td>ByteArrayInputStream<br>ByteArrayOutputStream</td>
        <td>CharArrayReader<br>CharArrayWriter</td>
    </tr>
    <tr>
        <td>PipedInputStream<br>PipedOutputStream</td>
        <td>PipedReader<br>PipedWriter</td>
    </tr>
    <tr>
        <td>StringBufferInputStream (deprecated)<br>StringBufferOutputStream (deprecated)</td>
        <td>StringReader<br>StringWriter</td>
    </tr>
</table>

(StringBufferInputStream, StringBufferOutputStream은 StringReader와 StringWriter로 대체되어 더 이상 사용되지 않는다.)

문자기반 스트림의 이름은 바이트기반 스트림의 이름에서 InputStream은 Reader로 OutputStream은 Writer로만 바꾸면 된다. 단, ByteArrayInputStream에 대응하는 문자기반 스트림은 char배열을 사용하는 CharArrayReader이다.