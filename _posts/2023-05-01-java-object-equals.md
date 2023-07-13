---
layout: post
title: Object클래스의 메서드 - equals()
description: Java의 정석 기초편 ch. 9 java.lang패키지와 유용한 클래스
categories: Java
date: 2023-05-01 14:17:00 +09:00
---
java.lang패키지는 자바 프로그래밍에서 가장 기본이 되는 클래스들을 포함하고 있다. 그렇기 때문에 java.lang패키지의 클래스들은 import문 없이도 사용할 수 있게 되어 있다. 그동안 String클래스나 System클래스를 import문 없이 사용할 수 있었던 이유가 바로 java.lang패키지에 속한 클래스들이기 때문이었던 것이다. 우선 java.lang패키지의 여러 클래스들 중에서도 자주 사용되는 클래스 몇 가지만을 골라서 학습해보자.


## Object클래스

Object클래스는 모든 클래스의 최고 조상이기 때문에 Object클래스의 멤버들은 모든 클래스에서 바로 사용 가능하다.

<table>
	<tr>
		<th>Object클래스의 메서드</th>
		<th>설명</th>
	</tr>
	<tr>
		<td>protected Object clone()</td>
		<td>객체 자신의 복사본을 반환한다.</td>
	</tr>
	<tr>
		<td>public boolean equals(Object obj)</td>
		<td>객체 자신과 객체 obj가 같은 객체인지 알려준다.(같으면 true)</td>
	</tr>
	<tr>
		<td>protected void finalize()</td>
		<td>객체가 소멸될 때 가비지 컬렉터에 의해 자동적으로 호출된다. 이때 수행되어야 하는 코드가 있을 때 오버라이딩한다. (거의 사용 안 함)</td>
	</tr>
	<tr>
		<td>public Class getClass()</td>
		<td>객체 자신의 클래스 정보를 담고 있는 Class인스턴스를 반환한다.</td>
	</tr>
	<tr>
		<td>public int hashCode()</td>
		<td>객체 자신의 해시코드를 반환한다.</td>
	</tr>
	<tr>
		<td>public String toString()</td>
		<td>객체 자신의 정보를 문자열로 반환한다.</td>
	</tr>
	<tr>
		<td>public void notify()</td>
		<td>객체 자신을 사용하려고 기다리는 쓰레드를 하나만 깨운다.</td>
	</tr>
	<tr>
		<td>public void notifyAll()</td>
		<td>객체 자신을 사용하려고 기다리는 모든 쓰레드를 깨운다.</td>
	</tr>
	<tr>
		<td>public void wait()<br>public void wait(long timeout)<br>public void wait(long timeout, int nanos)</td>
		<td>다른 쓰레드가 notify()나 notifyAll()을 호출할 때까지 현재 쓰레드를 무한히 또는 지정된 시간(timeout, nanos)동안 기다리게 한다. (timeout은 천 분의 1초, nanos는 10^9분의 1초)</td>
	</tr>
</table>

Object클래스는 멤버변수는 없고 오직 11개의 메서드만 가지고 있다. 이 메서드들은 모든 인스턴스가 가져야 할 기본적인 것들이며, 우선 이 중에서 중요한 몇 가지만 살펴볼 것이다.

(notify(), notifyAll(), wait()은 쓰레드(thread)와 관련된 것들이다.)


## Object클래스의 메서드 - equals()

매개변수로 객체의 참조변수를 받아서 비교하여 그 결과를 boolean값으로 알려주는 역할을 한다. 아래의 코드는 Object클래스에 정의되어 있는 equals메서드의 실제 내용이다.

```java
public boolean equals(Object obj) {
  return (this==obj);
}
```

위 코드에서 알 수 있듯이 두 객체의 같고 다름을 참조변수의 값으로 판단한다. 그렇기 때문에 **서로 다른 두 객체를 equals메서드로 비교하면 항상 false를 결과로 얻게 된다**.

(객체를 생성할 때, 메모리의 비어있는 공간을 찾아 생성하므로 서로 다른 두 개의 객체가 같은 주소를 갖는 일은 있을 수 없다. 그러나 두 개 이상의 참조변수가 같은 주솟값을 갖는 것(한 객체를 참조하는 것)은 가능하다.)

```java
class Ex9_1 {
	public static void main(String[] args) {
		Value v1 = new Value(10);
		Value v2 = new Value(10);

		if (v1.equals(v2))
			System.out.println("v1과 v2는 같습니다.");
		else
			System.out.println("v1과 v2는 다릅니다.");
	} // main
} 

class Value {
	int value;

	Value(int value) {
		this.value = value;
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
v1과 v2는 다릅니다.
```

value라는 멤버변수를 갖는 Value클래스를 정의하고, 두 개의 Value클래스의 인스턴스를 생성한 다음 equals메서드를 이용해서 두 인스턴스를 비교하도록 했다. **equals메서드는 주솟값으로 비교를 하기 때문에**, 두 Value인스턴스의 멤버변수 value의 값이 10으로 서로 같을지라도 equals메서드로 비교한 결과는 false일 수밖에 없는 것이다.

<img width="700" alt="[자바의 정석 - 기초편] ch9-1~3 Object클래스와 equals()" title="[자바의 정석 - 기초편] ch9-1~3 Object클래스와 equals()" src="https://user-images.githubusercontent.com/88493727/235413109-f81c5818-27aa-4ce8-8646-9edcadf6bb45.png">


**v1 == v2 -> 0x1234 == 0x2345 -> false**


## equals()의 오버라이딩

Object클래스로부터 상속받은 equals메서드는 결국 두 개의 참조변수가 같은 객체를 참족하고 있는지, 즉 두 참조변수에 저장된 값(주솟값)이 같은지를 판단하는 기능밖에 할 수 없다는 것을 알 수 있다.

만약 equals메서드로 Value인스턴스가 가지고 있는 value값을 비교하도록 하려면, Value클래스에서 equals메서드를 오버라이딩하여 주소가 아닌 객체에 저장된 내용을 비교하도록 변경하면 된다.

```java
class Person {
	long id;

	public boolean equals(Object obj) {
		if (obj instanceof Person) {
			return id == ((Person)obj).id;	// obj가 Object타입이므로 id값을 참조하기 위해서는 Person타입으로 형변환이 필요하다. 
		} else {
			return false;	// 타입이 Person이 아니면 값을 비교할 필요도 없다. 
		}
	}

	Person(long id) {
		this.id = id;
	}
}

class Ex9_2 {
	public static void main(String[] args) {
		Person p1 = new Person(8011081111222L);
		Person p2 = new Person(8011081111222L);

		if (p1.equals(p2))
			System.out.println("p1과 p2는 같은 사람입니다.");
		else
			System.out.println("p1과 p2는 다른 사람입니다.");
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
p1과 p2는 같은 사람입니다.
```

equals메서드가 Person인스턴스의 주솟값이 아닌 멤버변수 id의 값을 비교하도록 하기 위해 equals메서드를 오버라이딩 했다. 이렇게 함으로써 서로 다른 인스턴스일지라도 같은 id(주민등록번호)를 가지고 있다면 equals메서드로 비교했을 때 true를 결과로 얻게 할 수 있다. 

<img width="400" alt="[자바의 정석 - 기초편] ch9-1~3 Object클래스와 equals()" title="[자바의 정석 - 기초편] ch9-1~3 Object클래스와 equals()" src="https://user-images.githubusercontent.com/88493727/235412953-fb2ae3b1-94df-4b1c-8449-ade9ea243972.png">

**p1 == p2 -> 0x100 == 0x200 -> false**\
**p1.equals(p2) -> id == ((Person)obj).id -> 8011081111222L == 8011081111222L -> true**













