---
layout: post
title: 보조 스트림
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-02 23:12:00 +0900
---
이전 페이지에서 언급한 스트림 외에도 스트림의 기능을 보완하기 위한 보조 스트림이 제공된다. 보조 스트림은 실제 데이터를 주고받는 스트림이 아니기 때문에 데이터를 입출력할 수 있는 기능은 없지만, 스트림의 기능을 향상시키거나 새로운 기능을 추가할 수 있다. 그래서 보조 스트림만으로는 입출력을 처리할 수 없고, 스트림을 먼저 생성한 다음에 이를 이용해서 보조 스트림을 생성해야 한다.

예를 들어 test.txt라는 파일을 읽기 위해 FileInputStream을 사용할 때, 성능 향상을 위해 버퍼를 이용하는 보조 스트림인 BufferedInputStream을 사용하는 코드는 다음과 같다.

```java
// 먼저 기반 스트림을 생성한다.
FileInputStream fis = new FileInputStream("test.txt");

// 기반 스트림을 이용해서 보조 스트림을 생성한다.
BufferedInputStream bis = new BufferedInputStream(fis);
bis.read();     // 보조 스트림인 BufferedInputStream으로부터 데이터를 읽는다.
```

코드 상으로는 보조 스트림인 BufferedInputStream이 입력 기능을 수행하는 것처럼 보이지만, 실제 입력 기능은 BufferedInputStream과 연결된 FileInputStream이 수행하고, 보조 스트림인 BufferedInputStream은 버퍼만을 제공한다. 버퍼를 사용한 입출력과 사용하지 않은 입출력 간의 성능 차이는 상당하기 때문에 대부분의 경우에 버퍼를 이용한 보조 스트림을 사용한다.

BufferedInputStream, DataInputStream, DigestInputStream, LineNumberInputStream, PushbackInputStream은 모두 FilterInputStream의 자손들이고, FilterInputStream은 InputStream의 자손이라서 결국 모든 보조 스트림 역시 InputStream과 OutputStream의 자손들이므로 입출력방법이 같다.

<table>
    <tr>
        <th>입력</th>
        <th>출력</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>FilterInputStream</td>
        <td>FilterOutputStream</td>
        <td>필터를 이용한 입출력 처리</td>
    </tr>
    <tr>
        <td>BufferedInputStream</td>
        <td>BufferedOutputStream</td>
        <td>버퍼를 이용한 입출력 성능 향상</td>
    </tr>
    <tr>
        <td>DataInputStream</td>
        <td>DataOutputStream</td>
        <td>int, float와 같은 기본형 단위(primitive type)로 데이터를 처리</td>
    </tr>
    <tr>
        <td>SequenceInputStream</td>
        <td>없음</td>
        <td>두 개의 스트림을 하나로 연결</td>
    </tr>
    <tr>
        <td>LineNumberInputStream</td>
        <td>없음</td>
        <td>읽어 온 데이터의 라인 번호를 카운트 (JDK1.1부터 LineNumberReader로 대체)</td>
    </tr>
    <tr>
        <td>ObjectInputStream</td>
        <td>ObjectOutputStream</td>
        <td>데이터를 객체 단위로 읽고 쓰는 데 사용. 주로 파일을 이용하며 객체 직렬화와 관련 있음</td>
    </tr>
    <tr>
        <td>없음</td>
        <td>PrintStream</td>
        <td>버퍼를 이용하며, 추가적인 print관련 기능(print, printf, println메서드)</td>
    </tr>
    <tr>
        <td>PushbackInputStream</td>
        <td>없음</td>
        <td>버퍼를 이용해서 읽어 온 데이터를 다시 되돌리는 기능(unread)</td>
    </tr>
</table>