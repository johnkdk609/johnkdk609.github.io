---
layout: post
title: 문자 기반 스트림 - Reader, Writer
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-17 12:00:00 +0900
---
바이트기반 스트림의 조상이 InputStream/OutputStream인 것과 같이 문자기반의 스트림에서는 Reader/Writer가 그와 같은 역할을 한다. 다음은 Reader/Writer의 메서드인데 byte배열 대신 char배열을 사용한다는 것 외에는 InputStream/OutputStream의 메서드와 다르지 않다.

<table>
    <tr>
        <th>메서드</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>abstract void close()</td>
        <td>입력스트림을 닫음으로써 사용하고 있던 자원을 반환한다.</td>
    </tr>
    <tr>
        <td>void mark(int readlimit)</td>
        <td>현재위치를 표시해놓는다. 후에 reset()에 의해서 표시해 놓은 위치로 다시 돌아갈 수 있다.</td>
    </tr>
    <tr>
        <td>boolean markSupported()</td>
        <td>mark()와 reset()을 지원하는지를 알려준다.</td>
    </tr>
    <tr>
        <td>int read()</td>
        <td>입력소스로부터 하나의 문자를 읽어온다. char의 범위인 0~65535의 정수를 반환하며, 입력스트림의 마지막 데이터에 도달하면, -1을 반환한다.</td>
    </tr>
    <tr>
        <td>int read(char[] c)</td>
        <td>입력소스로부터 매개변수로 주어진 배열 c의 크기만큼 읽어서 배열 c에 저장한다. 읽어 온 데이터의 개수 또는 -1을 반환한다.</td>
    </tr>
    <tr>
        <td>abstract int read(char[] c, int off, int len)</td>
        <td>입력소스로부터 최대 len개의 문자를 읽어서, 배열 c의 지정된 위치(off)부터 읽은 만큼 저장한다. 읽어 온 데이터의 개수 또는 -1을 반환한다.</td>
    </tr>
    <tr>
        <td>int read(CharBuffer target)</td>
        <td>입력소스로부터 읽어서 문자버퍼(target)에 저장한다.</td>
    </tr>
    <tr>
        <td>boolean ready()</td>
        <td>입력소스로부터 데이터를 읽을 준비가 되어있는지 알려준다.</td>
    </tr>
    <tr>
        <td>void reset()</td>
        <td>입력소스에서의 위치를 마지막으로 mark()가 호출되었던 위치로 되돌린다.</td>
    </tr>
    <tr>
        <td>long skip(long n)</td>
        <td>현재 위치에서 주어진 문자 수(n)만큼을 건너뛴다.</td>
    </tr>
</table>

<table>
    <tr>
        <th>메서드</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>Writer append(char c)</td>
        <td>지정된 문자를 출력소스에 출력한다.</td>
    </tr>
    <tr>
        <td>Writer append(CharSequence c)</td>
        <td>지정된 문자열(CharSequence)을 출력소스에 출력한다.</td>
    </tr>
    <tr>
        <td>Writer append(CharSequence c, int start, int end)</td>
        <td>지정된 문자열(CharSequence)의 일부를 출력소스에 출력<br>(CharBuffer, String, StringBuffer가 CharSequence를 구현)</td>
    </tr>
    <tr>
        <td>abstract void close()</td>
        <td>출력스트림을 닫음으로써 사용하고 있던 자원을 반환한다.</td>
    </tr>
    <tr>
        <td>abstract void flush()</td>
        <td>스트림의 버퍼에 있는 모든 내용을 출력소스에 쓴다.(버퍼가 있는 스트림에만 해당됨)</td>
    </tr>
    <tr>
        <td>void write(int b)</td>
        <td>주어진 값을 출력소스에 쓴다.</td>
    </tr>
    <tr>
        <td>void write(char[] c)</td>
        <td>주어진 배열 c에 저장된 모든 내용을 출력소스에 쓴다.</td>
    </tr>
    <tr>
        <td>abstract void write(char[] c, int off, int len)</td>
        <td>주어진 배열 c에 저장된 내용 중에서 off번째부터 len길이만큼만 출력소스에 쓴다.</td>
    </tr>
    <tr>
        <td>void write(String str)</td>
        <td>주어진 문자열(str)을 출력소스에 쓴다.</td>
    </tr>
    <tr>
        <td>void write(String str, int off, int len)</td>
        <td>주어진 문자열(str)의 일부를 출력소스에 쓴다. (off번째 문자부터 len개 만큼의 문자열)</td>
    </tr>
</table>

한 가지 더 얘기하고 싶은 것은 문자기반 스트림이라는 것이 단순히 2 byte로 스트림을 처리하는 것만을 의미하지 않는다는 것이다. 문자 데이터를 다루는 데 필요한 또 하나의 정보는 인코딩(encoding)이다.

문자기반 스트림, 즉 Reader/Writer 그리고 그 자손들은 여러 종류의 인코딩과 자바에서 사용하는 유니코드(UTF-16) 간의 변환을 자동적으로 처리해준다. Reader는 특정 인코딩을 읽어서 유니코드로 변환하고 Writer는 유니코드를 특정 인코딩으로 변환하여 저장한다.