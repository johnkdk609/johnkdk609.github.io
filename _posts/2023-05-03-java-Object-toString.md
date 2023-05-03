---
layout: post
title: Object클래스의 메서드 - toString()
description: Java의 정석 기초편 ch. 9 java.lang패키지와 유용한 클래스
categories: Java
date: 2023-05-03 13:21:00 +09:00
---
toString메서드는 인스턴스에 대한 정보를 문자열(String)로 제공할 목적으로 정의한 것이다. 인스턴스의 정보를 제공한다는 것은 대부분의 경우 **인스턴스 변수에 저장된 값들을 문자열로 표현한다는 뜻**이다.

Object클래스에 정의된 toString()은 아래와 같다.

```java
public String toString() {
  return getClass().getName()+"@"+Integer.toHexString(hashCode());
}
```

클래스를 작성할 때 toString()을 오버라이딩하지 않는다면, 위와 같은 내용이 그대로 사용될 것이다. 즉, toString()을 호출하면 클래스 이름과 16진수의 해시코드를 얻게 될 것이다.

```java
class Card {
	String kind;
	int number;

	Card() {
		this("SPADE", 1);
	}

	Card(String kind, int number) {
		this.kind = kind;
		this.number = number;
	}
}

class Ex9_4 {
	public static void main(String[] args) {
		Card c1 = new Card();
		Card c2 = new Card();

		System.out.println(c1.toString());
		System.out.println(c2.toString());
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Card@6767c1fc
Card@29ee9faa
```

Card인스턴스를 두 개 생성한 다음, 각 인스턴스에 toString()을 호출한 결과를 출력했다. Card클래스에서 Object클래스로부터 상속받은 toString()을 오버라이딩하지 않았기 때문에 Card인스턴스에 toString()을 호출하면, Object클래스의 toString()이 호출된다. 

그래서 위의 결과에 클래스 이름과 해시코드가 출력되었다. **서로 다른 인스턴스에 대해서 toString()을 호출하였으므로 클래스의 이름은 같아도 해시코드 값이 다르다**는 것을 확인할 수 있다.


### toString()의 오버라이딩

String클래스의 toString()은 String인스턴스가 갖고 있는 문자열을 반환하도록 오버라이딩되어 있고, Date클래스의 경우, Date인스턴스가 갖고 있는 날짜와 시간을 문자열로 변환하여 반환하도록 오버라이딩 되어 있다. 

이처럼 **toString()은 일반적으로 인스턴스나 클래스에 대한 정보 또는 인스턴스 변수들의 값을 문자열로 변환하여 반환하도록 오버라이딩 되는 것이 보통이다**. 이제 Card클래스에서도 toString()을 오버라이딩해서 보다 쓸모 있는 정보를 제공할 수 있도록 바꿔보자. 

```java
class Card2 {
	String kind;
	int number;

	Card2() {
		this("SPADE", 1);  // Card(String kind, int number)를 호출 
	}

	Card2(String kind, int number) {
		this.kind = kind;
		this.number = number;
	}

	public String toString() {
		return "kind : " + kind + ", number : " + number;
	}
}

class Ex9_5 {
	public static void main(String[] args) {
		Card2 c1 = new Card2();
		Card2 c2 = new Card2("HEART", 10);
		System.out.println(c1.toString());
		System.out.println(c2.toString());
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
kind : SPADE, number : 1
kind : HEART, number : 10
```

Card2인스턴스의 toString()을 호출하면 인스턴스가 갖고 있는 인스턴스변수 kind와 number의 값을 문자열로 변환하여 반환하도록 toString()을 오버라이딩 했다. 오버라이딩할 때, Object클래스에 정의된 toString()의 접근 제어자가 public이므로 Card2클래스의 toString()의 접근제어자도 public으로 했다는 것을 눈여겨 봐야 한다. 

**조상에 정의된 메서드를 자손에서 오버라이딩할 때는 조상에 정의된 접근 범위보다 같거나 더 넓어야 하기 때문이다.** Object클래스에서 toString()의 접근 제어자가 public이므로, 이를 오버라이딩 하는 Card2클래스에서는 toString()의 접근 제어자를 public으로 해야 한다. 
