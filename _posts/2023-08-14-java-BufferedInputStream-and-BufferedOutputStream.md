---
layout: post
title: BufferedInputStream, BufferedOutputStream
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-14 13:55:00 +0900
---
BufferedInputStream/BufferedOutputStream은 스트림의 입출력 효율을 높이기 위해 버퍼를 사용하는 보조스트림이다. 한 바이트씩 입출력하는 것보다는 버퍼(바이트배열)를 이용해서 한 번에 여러 바이트를 입출력하는 것이 빠르기 때문에 대부분의 입출력 작업에 사용된다.


## BufferedInputStream

<table>
    <tr>
        <th>생성자</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>BufferedInputStream(InputStream in, int size)</td>
        <td>주어진 InputStream인스턴스를 입력소스(input source)로 하며 지정된 크기(byte단위)의 버퍼를 갖는 BufferedInputStream인스턴스를 생성한다.</td>
    </tr>
    <tr>
        <td>BufferedInputStream(InputStream in)</td>
        <td>주어진 InputStream인스턴스를 입력소스(input source)로 하며 버퍼의 크기를 지정해주지 않으므로 기본적으로 8192 byte 크기의 버퍼를 갖게 된다.</td>
    </tr>
</table>

BufferedInputStream의 버퍼크기는 입력소스로부터 한 번에 가져올 수 있는 데이터의 크기로 지정하면 좋다. 보통 입력소스가 파일인 경우 size의 값을 8192로 하는 것이 보통이며, 버퍼의 크기를 변경해가면서 테스트하면 최적의 버퍼크기를 알아낼 수 있다.

프로그램에서 입력소스로부터 데이터를 읽기 위해 처음으로 read메서드를 호출하면, BufferedInputStream은 입력소스로부터 버퍼 크기만큼의 데이터를 읽어다 자신의 내부 버퍼에 저장한다. 이제 프로그램에서는 BufferedInputStream의 버퍼에 저장된 데이터를 읽으면 되는 것이다. 외부의 입력소스로부터 읽는 것보다 내부의 버퍼로부터 읽는 것이 훨씬 빠르기 때문에 그만큼 작업 효율이 높아진다.

프로그램에서 버퍼에 저장된 모든 데이터를 다 읽고 그 다음 데이터를 읽기 위해 read메서드가 호출되면, BufferedInputStream은 입력소스로부터 다시 버퍼크기 만큼의 데이터를 읽어다 버퍼에 저장해 놓는다. 이와 같은 작업이 계속해서 반복된다.


## BufferedOutputStream

<table>
    <tr>
        <th>메서드 / 생성자</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>BufferedOutputStream(OutputStream out, int size)</td>
        <td>주어진 OutputStream인스턴스를 출력소스(output source)로 하며 지정된 크기(단위 byte)의 버퍼를 갖는 BufferedOutputStream인스턴스를 생성한다.</td>
    </tr>
    <tr>
        <td>BufferedOutputStream(OutputStream out)</td>
        <td>주어진 OutputStream인스턴스를 출력소스(output source)로 하며 버퍼의 크기를 지정해주지 않으므로 기본적으로 8192 byte 크기의 버퍼를 갖게 된다.</td>
    </tr>
    <tr>
        <td>flush()</td>
        <td>버퍼의 모든 내용을 출력소스에 출력한 다음, 버퍼를 비운다.</td>
    </tr>
    <tr>
        <td>close()</td>
        <td>flush()를 호출해서 버퍼의 모든 내용을 출력소스에 출력하고, BufferedOutputStream인스턴스가 사용하던 모든 자원을 반환한다.</td>
    </tr>
</table>

BufferedOutputStream 역시 버퍼를 이용해서 출력소스와 작업을 하게 되는데, 입력소스로부터 데이터를 읽을 때와는 반대로, 프로그램에서 write메서드를 이용한 출력이 BufferedOutputStream의 버퍼에 저장된다. 버퍼가 가득 차면, 그때 버퍼의 모든 내용을 출력소스에 출력한다. 그리고는 버퍼를 비우고 다시 프로그램으로부터의 출력을 저장할 준비를 한다.

버퍼가 가득 찼을 때만 출력소스에 출력을 하기 때문에, 마지막 출력부분이 출력소스에 쓰이지 못하고 BufferedOutputStream의 버퍼에 남아있는 채로 프로그램이 종료될 수 있다는 점을 주의해야 한다.

그래서 프로그램에서 모든 출력작업을 마친 후 BufferedOutputStream에 close()나 flush()를 호출해서 마지막에 버퍼에 있는 모든 내용이 출력소스에 출력되도록 해야 한다.

(BufferedOutputStream의 close()는 flush()를 호출하여 버퍼의 내용을 출력스트림에 쓰도록 한 후, BufferedOutputStream인스턴스의 참조변수에 null을 지정함으로써 사용하던 자원들이 반환되게 한다.)


## BufferedOutputStream 예제

```java
import java.io.*;

class Ex15_6 {
	public static void main(String args[]) {
		try {
		     FileOutputStream fos = new FileOutputStream("123.txt");
		     // BufferedOutputStream의 버퍼 크기를 5로 한다. 
		     BufferedOutputStream bos = new BufferedOutputStream(fos, 5);
		     // 파일 123.txt에 1부터 9까지 출력한다. 
		     for(int i='1'; i <= '9'; i++) {
			     bos.write(i);
		     }

		     fos.close();  // FileOutputStream을 닫는다. 
		} catch (IOException e) {
		     e.printStackTrace();		
		}
	}
}
```

크기가 5인 BufferedOutputStream을 이용해서 파일 123.txt에 1부터 9까지 출력하는 예제인데 결과를 보면 5까지만 출력된 것을 알 수 있다. 그 이유는 버퍼에 남아있는 데이터가 출력되지 못한 상태로 프로그램이 종료되었기 때문이다.

이 예제에서 fos.close()를 호출해서 스트림을 닫아주기는 했지만, 이렇게 해서는 BufferedOutputStream의 버퍼에 있는 내용이 출력되지 않는다. bos.close();로 BufferedOutputStream의 close()를 호출해 주어야 버퍼에 남아있던 모든 내용이 출력된다. BufferedOutputStream의 close()는 기반 스트림인 FileOutputStream의 close()를 호출하기 때문에 FileOutputStream의 close()는 따로 호출해주지 않아도 된다.

아래의 코드는 BufferedOutputStream의 조상인 FilterOutputStream의 소스코드인데 FilterOutputStream에 정의된 close()는 flush()를 호출한 다음에 기반스트림의 close()를 호출하는 것을 알 수 있다. BufferedOutputStream은 FilterOutputStream의 close()를 오버라이딩 없이 그대로 상속받는다.

```java
public class FilterOutputStream extends OutputStream {
    protected OutputStream out;

    public FilterOutputStream(OutputStream out) {
        this.out = out;
    }
        ...
    public void close() throws IOException {
        try {
            flush();
        } catch (IOException ignored) {}
        out.close();    // 기반 스트림의 close()를 호출한다.
    }
}
```

이처럼 보조 스트림을 사용한 경우에는 기반 스트림의 close()나 flush()를 호출할 필요 없이 단순히 보조 스트림의 close()를 호출하기만 하면 된다.

만일 이 예제가 생성하는 '123.txt' 파일이 이클립스에서 보이지 않는다면, Package Explorer를 클릭하고 새로고침하면 나타날 것이다.