---
layout: post
title: FilterInputStream과 FilterOutputStream
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-09 15:40:00 +0900
---
FilterInputStream/FilterOutputStream은 InputStream/OutputStream의 자손이면서 모든 보조 스트림의 조상이다. 보조 스트림은 자체적으로 입출력을 수행할 수 없기 때문에 기반 스트림을 필요로 한다. 다음은 FilterInputStream/FilterOutputStream의 생성자다.

> **protected** - FilterInputStream(InputStream in)\
> **public** - FilterOutputStream(OutputStream out)

FilterInputStream/FilterOutputStream의 모든 메서드는 단순히 기반 스트림의 메서드를 그대로 호출할 뿐이다. FilterInputStream/FilterOutputStream 자체로는 아무런 일도 하지 않음을 의미한다. FilterInputStream/FilterOutputStream은 상속을 통해 원하는 작업을 수행하도록 읽고 쓰는 메서드를 오버라이딩해야 한다.

```java
public class FilterInputStream extends InputStream {
    protected volatile InputStream in;

    protected FilterInputStream(InputStream in) {
        this.in = in;
    }

    public int read() throws IOException {
        return in.read();
    }
    ...
}
```

생성자 FilterInputStream(InputStream in)는 접근 제어자가 protected이기 때문에 Filter InputStream의 인스턴스를 생성해서 사용할 수 없고 상속을 통해서 오버라이딩되어야 한다.

FilterInputStream/FilterOutputStream을 상속받아서 기반스트림에 보조기능을 추가한 보조스트림 클래스는 다음과 같다.

> **FilterInputStream의 자손** - BufferedInputStream, DataInputStream, PushbackInputStream 등\
> **FilterOutputStream의 자손** - BufferedOutputStream, DataOutputStream, PrintStream 등