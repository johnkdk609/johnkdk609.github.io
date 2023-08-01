---
layout: post
title: 바이트 기반 스트림 - InputStream, OutputStream
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-01 23:19:00 +0900
---
스트림은 바이트 단위로 데이터를 전송하며 입출력 대상에 따라 다음과 같은 입출력 스트림이 있다.

<table>
    <tr>
        <th>입력스트림</th>
        <th>출력스트림</th>
        <th>입출력 대상의 종류</th>
    </tr>
    <tr>
        <td>FileInputStream</td>
        <td>FileOutputStream</td>
        <td>파일</td>
    </tr>
    <tr>
        <td>ByteArrayInputStream</td>
        <td>ByteArrayOutputStream</td>
        <td>메모리(byte배열)</td>
    </tr>
    <tr>
        <td>PipedInputStream</td>
        <td>PipedOutputStream</td>
        <td>프로세스(프로세스 간의 통신)</td>
    </tr>
    <tr>
        <td>AudioInputStream</td>
        <td>AudioOutputStream</td>
        <td>오디오 장치</td>
    </tr>
</table>

위의 표와 같이 여러 종류의 입출력 스트림이 있으며, 어떠한 대상에 대해서 작업을 할 것인지 그리고 입력을 할 것인지 출력을 할 것인지에 따라서 해당 스트림을 선택해서 사용하면 된다. 예를 들어 어떤 파일의 내용을 읽고자 하는 경우 FileInputStream을 사용하면 될 것이다.

이들은 모두 InputStream 또는 OutputStream의 자손들이며, 각각 읽고 쓰는 데 필요한 추상 메서드를 자신에 맞게 구현해 놓았다.

자바에서는 java.io패키지를 통해서 많은 종류의 입출력 관련 클래스들을 제공하고 있으며, 입출력을 처리할 수 있는 표준화된 방법을 제공함으로써 입출력의 대상이 달라져도 동일한 방법으로 입출력이 가능하기 때문에 프로그래밍을 하기에 편리하다.

<table>
    <tr>
        <th>InputStream</th>
        <th>OutputStream</th>
    </tr>
    <tr>
        <td>abstract int read()</td>
        <td>abstract void write(int b)</td>
    </tr>
    <tr>
        <td>int read(byte[] b)</td>
        <td>void write(byte[] b)</td>
    </tr>
    <tr>
        <td>int read(byte[] b, int off, int len)</td>
        <td>void write(byte[] b, int off, int len)</td>
    </tr>
</table>

(read()의 반환 타입이 byte가 아니라 int인 이유는 read()의 반환값의 범위가 0~255와 -1이기 때문이다.)

위의 표에서 나온 메서드의 사용법만 잘 알고 있어도 데이터를 읽고 쓰는 것은 입출력 대상의 종류에 관계없이 아주 간단한 일이 될 것이다.

InputStream의 read()와 OutputStream의 write(int b)는 입출력의 대상에 따라 읽고 쓰는 방법이 다를 것이기 때문에 각 상황에 알맞게 구현하라는 의미에서 추상메서드로 정의되어 있다.