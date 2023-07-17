---
layout: post
title: 제한된 제네릭 클래스, 제네릭스의 제약
description: Java의 정석 기초편 ch. 12 지네릭스, 열거형, 애너테이션
categories: Java
date: 2023-06-01 17:02:00 +09:00
---
타입 문자로 사용할 타입을 명시하면 한 종류의 타입만 저장할 수 있도록 제한할 수 있지만, 그래도 여전히 모든 종류의 타입을 지정할 수 있다는 것에는 변함이 없다. 그렇다면, 타입 매개변수 T에 지정할 수 있는 타입의 종류를 제한할 수 있는 방법은 없을까?

```java
FruitBox<Toy> fruitBox = new FruitBox<Toy>();
fruitBox.add(new Toy());      // OK. 과일 상자에 장난감을 담을 수 있다.
```

다음과 같이 제네릭 타입에 'extends'를 사용하면, 특정 타입의 자손들만 대입할 수 있게 제한할 수 있다.

```java
class FruitBox<T extends Fruit> {   // Fruit의 자손만 타입으로 지정 가능
  ArrayList<T> list = new ArrayList<T>();
  ...
}
```

여전히 한 종류의 타입만 담을 수 있지만, Fruit클래스의 자손들만 담을 수 있다는 제한이 더 추가된 것이다.

```java
FruitBox<Apple> appleBox = new FruitBox<Apple>();   // OK
FruitBox<Toy> toyBox = new FruitBox<Toy>();         // ERROR. Toy는 Fruit의 자손이 아님
```

만일 클래스가 아니라 인터페이스를 구현해야 한다는 제약이 필요하다면, 이때도 'extends'를 사용한다. 'implements'를 사용하지 않는다는 점에 주의해야 한다.

```java
interface Eatable {}
class FruitBox<T extends Eatable> { ... }
```

클래스 Fruit의 자손이면서 Eatable인터페이스도 구현해야 한다면 아래와 같이 '&' 기호로 연결한다.

```java
class FruitBox<T extends Fruit & Eatable> { ... }
```


## 제한된 제네릭 클래스 예제

```java
import java.util.ArrayList;

class Fruit implements Eatable {
	public String toString() { return "Fruit"; }
}
class Apple extends Fruit { public String toString() { return "Apple"; }}
class Grape extends Fruit { public String toString() { return "Grape"; }}
class Toy                 { public String toString() { return "Toy"  ; }}

interface Eatable {}

class Ex12_3 {
	public static void main(String[] args) {
		FruitBox<Fruit> fruitBox = new FruitBox<Fruit>();
		FruitBox<Apple> appleBox = new FruitBox<Apple>();
		FruitBox<Grape> grapeBox = new FruitBox<Grape>();
//		FruitBox<Grape> grapeBox = new FruitBox<Apple>(); // 에러. 타입 불일치 
//		FruitBox<Toy>   toyBox   = new FruitBox<Toy>();   // 에러. 

		fruitBox.add(new Fruit());
		fruitBox.add(new Apple());
		fruitBox.add(new Grape());
		appleBox.add(new Apple());
//		appleBox.add(new Grape());  // 에러. Grape는 Apple의 자손이 아님 
		grapeBox.add(new Grape());

		System.out.println("fruitBox-" + fruitBox);
		System.out.println("appleBox-" + appleBox);
		System.out.println("grapeBox-" + grapeBox);
	}  // main
}

class FruitBox<T extends Fruit & Eatable> extends Box<T> {}

class Box<T> {
	ArrayList<T> list = new ArrayList<T>();
	void add(T item) { list.add(item);     }
	T get(int i)     { return list.get(i); }
	int size()       { return list.size(); }
	public String toString() { return list.toString();}
}
```

위 코드의 출력 결과는 다음과 같다.

```
fruitBox-[Fruit, Apple, Grape]
appleBox-[Apple]
grapeBox-[Grape]
```


## 제네릭스의 제약

제네릭 클래스 Box의 객체를 생성할 때, 객체별로 다른 타입을 지정하는 것은 적절하다. 제네릭스는 이처럼 인스턴스별로 다르게 동작하도록 만든 기능이기 때문이다.

```java
Box<Apple> appleBox = new Box<Apple>();   // OK. Apple객체만 저장 가능
Box<Grape> grapeBox = new Box<Grape>();   // OK. Grape객체만 저장 가능
```

그러나 모든 객체에 대해 동일하게 동작해야 하는 static멤버에 타입 변수 T를 사용할 수 없다. T는 인스턴스변수로 간주되기 때문이다. 이미 알고 있듯이, static멤버는 인스턴스변수를 참조할 수 없다.

```java
class Box<T> {
  static T item;    // ERROR
  static int compare(T t1, T t2) { ... }    // ERROR
    ...
}
```

static멤버는 타입 변수에 지정된 타입, 즉 대입된 타입의 종류에 관계없이 동일한 것이어야 하기 때문이다. 즉, 'Box\<Apple\>.item'과 'Box\<Grape\>.item'이 다른 것이어서는 안 된다는 뜻이다. 

그리고 제네릭 타입의 배열을 생성하는 것도 허용되지 않는다. 제네릭 배열 타입의 참조변수를 선언하는 것은 가능하지만, 'new T\[10\]'과 같이 배열을 생성하는 것은 안 된다는 뜻이다. 

```java
class Box<T> {
  T[] itemArr;    // OK. T타입의 배열을 위한 참조변수
    ...
  T[] toArray() {
    T[] tmpArr = new T[itemArr.length];   // ERROR. 제네릭 배열 생성 불가
    ...
    return tmpArr;
  }
    ...
}
```

제네릭 배열을 생성할 수 없는 것은 new연산자 때문인데, 이 연산자는 컴파일 시점에 타입 T가 뭔지 정확히 알아야 한다. 그런데 위의 코드에 정의된 Box\<T\>클래스를 컴파일하는 시점에서는 T가 어떤 타입이 될지 전혀 알 수 없다. instanceof연산자도 new연산자와 같은 이유로 T를 피연산자로 사용할 수 없다. 
