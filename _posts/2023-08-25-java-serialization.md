---
layout: post
title: 직렬화(serialization)
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-25 19:20:00 +0900
---
직렬화(serialization)란 객체를 데이터 스트림으로 만드는 것을 뜻한다. 다시 얘기하면 객체에 저장된 데이터를 스트림에 쓰기(write) 위해 연속적인(serial) 데이터로 변환하는 것을 말한다.

반대로 스트림으로부터 데이터를 읽어서 객체를 만드는 것을 역직렬화(deserialization)라고 한다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f12285b2-e21f-43b5-a14b-f15f13cfbf16)

직렬화라는 용어 때문에 괜히 어렵게 느껴질 수 있는데 사실 객체를 저장하거나 전송하려면 당연히 이렇게 할 수밖에 없다.

이미 앞서 객체에 대해서 설명했지만, 여기서 객체란 무엇이며, 객체를 저장한다는 것은 무엇을 의미하는가에 대해서 다시 한 번 정리하고 넘어가는 것이 좋을 것 같다.

객체는 클래스에 정의된 인스턴스변수의 집합이다. 객체에는 클래스변수나 메서드가 포함되지 않는다. 객체는 오직 인스턴스변수들로만 구성되어 있다.

전에는 이해를 돕기 위해 객체를 생성하면 인스턴스변수와 메서드를 함께 그리곤 했지만 사실 객체에는 메서드가 포함되지 않는다. 인스턴스변수는 인스턴스마다 다른 값을 가질 수 있어야 하기 때문에 별도의 메모리공간이 필요하지만 메서드는 변하는 것이 아니라서 메모리를 낭비해 가면서 인스턴스마다 같은 내용의 코드(메서드)를 포함시킬 이유는 없다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/fe0d6c50-76ab-425c-bc7d-8de37be771fa)

그래서 객체를 저장한다는 것은 객체의 모든 인스턴스변수의 값을 저장한다는 것과 같은 의미이다. 어떤 객체를 저장하고자 한다면, 현재 객체의 모든 인스턴스변수의 값을 저장하기만 하면 된다. 그리고 저장했던 객체를 다시 생성하려면, 객체를 생성한 후에 저장했던 값을 읽어서 생성한 객체의 인스턴스변수에 저장하면 되는 것이다.


## ObjectInputStream, ObjectOutputStream

직렬화(스트림에 객체를 출력)에는 ObjectOutputStream을 사용하고 역직렬화(스트림으로부터 객체를 입력)에는 ObjectInputStream을 사용한다.

ObjectInputStream과 ObjectOutputStream은 각각 InputStream과 OutputStream을 직접 상속받지만 기반스트림을 필요로 하는 보조스트림이다. 그래서 객체를 생성할 때 입출력(직렬화/역직렬화)할 스트림을 지정해주어야 한다.

```java
ObjectInputStream(InputStream in)
ObjectOutputStream(OutputStream out)
```

만약 파일에 객체를 저장(직렬화)하고 싶다면 다음과 같이 하면 된다.

```java
FileOutputStream fos = new FileOutputStream("objectfile.ser");
ObjectOutputStream out = new ObjectOutputStream(fos);

out.writeObject(new UserInfo());
```

위의 코드는 objectfile.ser이라는 파일에 UserInfo객체를 직렬화하여 저장한다. 출력할 스트림(FileOutputStream)을 생성해서 이를 기반스트림으로 하는 ObjectOutputStream을 생성한다. ObjectOutputStream의 writeObject(Obejct obj)를 사용해서 객체를 출력하면, 객체가 파일에 직렬화되어 저장된다.

역직렬화 방법 역시 간단하다. 직렬화할 때와는 달리 입력스트림을 사용하고 writeObject(Object obj) 대신 readObject()를 사용하여 저장된 데이터를 읽기만 하면 객체로 역직렬화된다.

다만 readObject()의 반환타입이 Object이기 때문에 객체 원래의 타입으로 형변환 해주어야 한다.

```java
FileInputStream fis = new FileInputStream("obejctfile.ser");
ObjectInputStream in = new ObjectInputStream(fis);

UserInfo info = (UserInfo)in.readObject();
```

ObjectInputStream과 ObjectOutputStream에는 readObject()와 writeObject() 이외에도 여러 가지 타입의 값을 입출력할 수 있는 메서드를 제공한다.

<table>
    <tr>
        <th>ObjectInputStream</th>
        <th>ObjectOutputStream</th>
    </tr>
    <tr>
        <td>void defaultReadObject()<br>int read()<br>int read(byte[] buf, int off, int len)<br>boolean readBoolean()<br>byte readByte()<br>char readChar()<br>double readDouble()<br>float readFloat()<br>int readInt()<br>long readLong()<br>short readShort()<br>Object readObject()<br>int readUnsignedByte()<br>int readUnsignedShort()<br>Object readUnshared()<br>String readUTF()</td>
        <td>void defaultWriteObject()<br>void write(byte[] buf)<br>void write(byte[] buf, int off, int len)<br>void write(int val)<br>void writeBoolean(boolean val)<br>void writeByte(int val)<br>void writeBytes(String str)<br>void writeChar(int val)<br>void writeChars(String str)<br>void writeDouble(double val)<br>void writeFloat(float val)<br>void writeInt(int val)<br>void writeLong(long val)<br>void writeObject(Object obj)<br>void writeShort(int val)<br>void writeUnshared(Object obj)<br>void writeUTF(String str)</td>
    </tr>
</table>

이 메서드들은 직렬화와 역직렬화를 직접 구현할 때 주로 사용되며, defaultReadObject()와 defaultWriteObject()는 자동 직렬화를 수행한다.

객체를 직렬화/역직렬화하는 작업은 객체의 모든 인스턴스변수가 참조하고 있는 모든 객체에 대한 것이기 때문에 상당히 복잡하며 시간도 오래 걸린다. readObject()와 writeObject()를 사용한 자동 직렬화가 편리하기는 하지만 직렬화작업 시간을 단축시키려면 직렬화하고자 하는 객체의 클래스에 추가적으로 다음과 같은 2개의 메서드를 직접 구현해주어야 한다.

```java
private void writeObject(ObjectOutputStream out)
    throws IOException {
    // write메서드를 사용해서 직렬화를 수행한다.
}

private void readObject(ObjectInputStream in)
    throws IOException, ClassNotFoundException {
    // read메서드를 사용해서 역직렬화를 수행한다.
}
```