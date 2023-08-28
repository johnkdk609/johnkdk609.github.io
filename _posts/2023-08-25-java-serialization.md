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


## 직렬화가 가능한 클래스 만들기

직렬화가 가능한 클래스를 만드는 방법은 간단하다. 직렬화하고자 하는 클래스가 java.io.Serializable인터페이스를 구현하도록 하면 된다.

예를 들어 아래 보기 1과 같이 UserInfo라는 클래스가 있을 때, 이 클래스를 직렬화가 가능하도록 변경하려면 아래 보기 2와 같이 Serializable인터페이스를 구현하도록 변경하면 된다.

```java
// 보기 1

public class UserInfo {
    String name;
    String password;
    int age;
}
```

↓

```java
// 보기 2

public class UserInfo implements java.io.Serializable {
    String name;
    String password;
    int age;
}
```

Serializable인터페이스는 아무런 내용도 없는 빈 인터페이스이지만, 직렬화를 고려하여 작성한 클래스인지를 판단하는 기준이 된다.

```java
public interface Serializable { }
```

아래와 같이 Serializable을 구현한 클래스를 상속받는다면, Serializable을 구현하지 않아도 된다. UserInfo는 Serializable을 구현하지 않았지만 조상인 SuperUserInfo가 Serializable를 구현하였으므로 UserInfo 역시 직렬화가 가능하다.

```java
public class SuperUserInfo implements Serializable {
    String name;
    String password;
}

public class UserInfo extends SuperUserInfo {
    int age;
}
```

그러나 다음과 같이 조상클래스가 Serializable을 구현하지 않았다면 자손클래스를 직렬화할 때 조상클래스에 정의된 인스턴스변수 name과 password는 직렬화 대상에서 제외된다.

```java
public class SuperUserInfo {
    String name;        // 직렬화 대상에서 제외
    String password;    // 직렬화 대상에서 제외
}

public class UserInfo extends SuperUserInfo implements Serializable {
    int age;
}
```


## 직렬화 대상에서 제외시키기 - transient

아래의 UserInfo클래스는 Serializable을 구현하고 있지만, 이 클래스의 객체를 직렬화하면 java.io.NotSerializableException이 발생하면서 직렬화에 실패한다. 그 이유는 직렬화할 수 없는 클래스의 객체를 인스턴스변수가 참조하고 있기 때문이다.

모든 클래스의 최고조상인 Object는 Serializable을 구현하지 않았기 때문에 직렬화할 수 없다. 만일 Object가 Serializable을 구현했다면 모든 클래스가 직렬화될 수 있을 것이다.

```java
public class UserInfo implements Serializable {
    String name;
    String password;
    int age;
    Object obj = new Object();      // Object객체는 직렬화할 수 없다.
}
```

위의 경우와 비교해서 다음과 같은 경우에는 직렬화가 가능하다는 것을 알아두자. 인스턴스변수 obj의 타입이 직렬화가 안 되는 Object이기는 하지만 실제로 저장된 객체는 직렬화가 가능한 String인스턴스이기 때문에 직렬화가 가능하다.

인스턴스변수의 타입이 아닌 실제로 연결된 객체의 종류에 의해서 결정된다는 것을 기억해야 한다.

```java
public class UserInfo implements Serializable {
    String name;
    String password;
    int age;
    Object obj = new String("abc");     // String은 직렬화될 수 있다.
}
```

직렬화하고자 하는 객체의 클래스에 직렬화가 안 되는 객체에 대한 참조를 포함하고 있다면, 제어자 transient를 붙여서 직렬화 대상에서 제외되도록 할 수 있다.

또는 password와 같이 보안상 직렬화되면 안 되는 값에 대해서 transient를 사용할 수 있다. 다르게 표현하면 transient가 붙은 인스턴스변수의 값은 그 타입의 기본값으로 직렬화된다고 볼 수 있다.

즉, UserInfo객체를 역직렬화하면 참조변수인 obj와 password의 값은 null이 된다.

```java
public class UserInfo implements Serializable {
    String name;
    transient String password;      // 직렬화 대상에서 제외된다.
    int age;
    transient Object obj = new Object();    // 직렬화 대상에서 제외된다.
}
```


## 직렬화와 역직렬화 예제 1

아래 예제는 예제15-20에 사용될 UserInfo클래스의 소스이다. 그래서 예제15-20을 실행하기 전에 아래 예제를 먼저 컴파일해야 한다.

```java
public class UserInfo implements java.io.Serializable {
	String name;
	String password;
	int age;

	public UserInfo() {
		this("Unknown", "1111", 0);
	}

	public UserInfo(String name, String password, int age) {
		this.name = name;	
		this.password = password;	
		this.age = age;	
	}

	public String toString() {
		return "("+ name + "," + password + "," + age + ")";
	}
}
```


## 직렬화와 역직렬화 예제 2

```java
import java.io.*;
import java.util.ArrayList;

public class Ex15_20 {
	public static void main(String[] args) {
		try {
			String fileName = "UserInfo.ser";
			FileOutputStream     fos = new FileOutputStream(fileName);
			BufferedOutputStream bos = new BufferedOutputStream(fos);

			ObjectOutputStream out = new ObjectOutputStream(bos);
			
			UserInfo u1 = new UserInfo("JavaMan","1234",30);
			UserInfo u2 = new UserInfo("JavaWoman","4321",26);

			ArrayList<UserInfo> list = new ArrayList<>();
			list.add(u1);
			list.add(u2);

			// 객체를 직렬화한다. 
			out.writeObject(u1);
			out.writeObject(u2);
			out.writeObject(list);
			out.close();
			System.out.println("직렬화가 잘 끝났습니다.");
		} catch (IOException e) {
			e.printStackTrace();
		}
	} // main
} // class
```

위 코드의 출력 결과는 다음과 같다.

```
직렬화가 잘 끝났습니다.
```

생성한 객체를 직렬화하여 파일(UserInfo.ser)에 저장하는 예제이다. 버퍼를 이용한 FileOutputStream을 기반으로 하는 ObjectOutputStream을 생성한 다음, writeObject()를 이용해서 객체를 ObjectInputStream에 출력하면 UserInfo.ser 파일에 객체가 직렬화되어 저장된다.

객체를 직렬화하는 코드는 이처럼 허무하게 간단하지만, 객체에 정의된 모든 인스턴스변수에 대한 참조를 찾아들어가기 때문에 상당히 복잡하고 시간이 걸리는 작업이 될 수 있다.

이 예제처럼 ArrayList와 같은 객체를 직렬화하면 ArrayList에 저장된 모든 객체들과 각 객체의 인스턴스변수가 참조하고 있는 객체들까지 모두 직렬화된다.

(확장자를 직렬화(serialization)의 약자인 'ser'로 하는 것이 보통이지만 이에 대한 제약은 없다.)


## 직렬화와 역직렬화 예제 3

```java
import java.io.*;
import java.util.ArrayList;

public class Ex15_21 {
	public static void main(String[] args) {
		try {
			String fileName = "UserInfo.ser";
			FileInputStream     fis = new FileInputStream(fileName);
			BufferedInputStream bis = new BufferedInputStream(fis);

			ObjectInputStream in = new ObjectInputStream(bis);

			// 객체를 읽을 때는 출력한 순서와 일치해야 한다. 
			UserInfo u1 = (UserInfo)in.readObject();
			UserInfo u2 = (UserInfo)in.readObject();
			ArrayList list = (ArrayList)in.readObject();

			System.out.println(u1);
			System.out.println(u2);
			System.out.println(list);
			in.close();
		} catch(Exception e) {
			e.printStackTrace();
		}
	} // main
} // class
```

위 코드의 출력 결과는 다음과 같다.

```
(JavaMan,1234,30)
(JavaWoman,4321,26)
[(JavaMan,1234,30), (JavaWoman,4321,26)]
```

이 전의 예제에서 직렬화한 객체를 역직렬화하는 예제이다. 이전과 반대로 FileInputStream과 ObjectInputStream을, 그리고 writeObject() 대신 readObject()를 사용했다는 점을 제외하고는 거의 같다.

ObjectInputStream의 readObject()로 직렬화한 객체를 역직렬화하였는데, readObject()의 리턴타입이 Object이므로 원래의 타입으로 형변환을 해주어야 한다.

한 가지 주의해야할 점은 객체를 역직렬화할 때는 직렬화할 때의 순서와 일치해야 한다는 것이다. 예를 들어 객체 u1, u2, list의 순서로 직렬화 했다면, 역직렬화할 때도 u1, u2, list의 순서로 처리해야 한다.

그래서 직렬화할 객체가 많을 때는 각 객체를 개별적으로 직렬화하는 것보다 ArrayList와 같은 컬렉션에 저장해서 직렬화하는 것이 좋다. 역직렬화할 때 ArrayList 하나만 역직렬화하면 되므로 역직렬화할 객체의 순서를 고려하지 않아도 되기 때문이다.