---
layout: post
title: 와일드 카드, 제네릭 메서드
description: Java의 정석 기초편 ch. 12 지네릭스, 열거형, 애너테이션
categories: Java
date: 2023-06-02 16:21:00 +09:00
---
제네릭 클래스를 생성할 때, 참조변수에 지정된 제네릭 타입과 생성자에 지정된 제네릭 타입은 일치해야 한다.

```java
ArrayList<Tv> list = new ArrayList<Tv>();   // OK. 제네릭 타입 일치
List<Tv> list = new ArrayList<Tv>();        // OK. 다형성. 제네릭 타입 일치
```

만일 일치하지 않으면 다음과 같이 컴파일 에러가 발생한다. Product와 Tv가 서로 상속관계라도 마찬가지이다.

```java
ArrayList<Product> list = new ArrayList<Tv>();    // ERROR. 제네릭 타입 불일치
```

그러면 제네릭 타입에 다형성을 적용할 방법은 없을까? 제네릭 타입으로 '**와일드 카드**'를 사용하면 된다. 와일드 카드는 기호 '?'를 사용하는데 다음과 같이 'extends'와 'super'로 상한(upper bound)과 하한(lower bound)을 제한할 수 있다.

> **&lt;? extends T&gt;** - 와일드 카드의 상한 제한. T와 그 자손들만 가능\
> **&lt;? super T&gt;** - 와일드 카드의 하한 제한. T와 그 조상들만 가능\
> **&lt;?&gt;** - 제한 없음. 모든 타입이 가능. &lt;? extends Object&gt;와 동일

와일드 카드를 이용하면 다음과 같이 하나의 참조변수로 다른 제네릭 타입이 지정된 객체를 다룰 수 있다. (Tv와 Audio가 Product의 자손이라고 가정)

```java
// 제네릭 타입이 '? extends Product'이면, Product와 Product의 모든 자손이 OK
ArrayList<? extends Product> list = new ArrayList<Tv>();      // OK
ArrayList<? extends Product> list = new ArrayList<Audio>();   // OK
```

와일드 카드를 아래와 같이 메서드의 매개변수에 적용하면,

```java
static Juice makeJuice(FruitBox<? extends Fruit> box) {
  String tmp = "";
  for (Fruit f : box.getList()) tmp += f + " ";
  return new Juice(tmp);
}
```

다음과 같이 제네릭 타입이 다른 여러 객체를 매개변수로 지정할 수 있다. (Apple이 Fruit의 자손이라고 가정)

```java
System.out.println(Juicer.makeJuice(new FruitBox<Fruit>()));    // OK
System.out.println(Juicer.makeJuice(new FruitBox<Apple>()));    // OK
```


## 와일드 카드 예제

```java
import java.util.ArrayList;

class Fruit2		       	{ public String toString() { return "Fruit"; }}
class Apple2 extends Fruit2	{ public String toString() { return "Apple"; }}
class Grape2 extends Fruit2	{ public String toString() { return "Grape"; }}

class Juice {
	String name;

	Juice(String name)       { this.name = name + "Juice"; }
	public String toString() { return name;                }
}

class Juicer {
	static Juice makeJuice(FruitBox2<? extends Fruit2> box) {
		String tmp = "";

		for (Fruit2 f : box.getList()) 
			tmp += f + " ";
		return new Juice(tmp);
	}
}

class Ex12_4 {
	public static void main(String[] args) {
		FruitBox2<Fruit2> fruitBox = new FruitBox2<Fruit2>();
		FruitBox2<Apple2> appleBox = new FruitBox2<Apple2>();

		fruitBox.add(new Apple2());
		fruitBox.add(new Grape2());
		appleBox.add(new Apple2());
		appleBox.add(new Apple2());

		System.out.println(Juicer.makeJuice(fruitBox));
		System.out.println(Juicer.makeJuice(appleBox));
	}  // main
}

class FruitBox2<T extends Fruit2> extends Box2<T> {}

class Box2<T> {
	ArrayList<T> list = new ArrayList<T>();
	void add(T item) { list.add(item);      }
	T get(int i)     { return list.get(i);  }
	ArrayList<T> getList() { return list;   }
	int size()       { return list.size();  }
	public String toString() { return list.toString(); }
}
```

위 코드의 출력 결과는 다음과 같다.

```
Apple Grape Juice
Apple Apple Juice
```


## 제네릭 메서드

메서드의 선언부에 제네릭 타입이 선언된 메서드를 **제네릭 메서드**라 한다. 앞서 살펴본 것처럼, Collections.sort()가 바로 제네릭 메서드이며, 제네릭 타입의 선언 위치는 반환 타입 바로 앞이다.

```java
static <T> void sort(List<T> list, Comparator<? super T> c)
```

제네릭 클래스에 정의된 타입 매개변수가 T이고 제네릭 메서드에 정의된 타입 매개변수가 T이어도 이 둘은 전혀 별개의 것이다. 같은 타입 문자 T를 사용해도 같은 것이 아니라는 것에 주의해야 한다. 참고로 제네릭 메서드는 제네릭 클래스가 아닌 클래스에도 정의될 수 있다. 

```java
class FruitBox<T> {
    ...
  static <T> void sort(List<T> list, Comparator<? super T> c) {
    ...
  }
}
```

위의 코드에서 제네릭 클래스 FruitBox에 선언된 타입 매개변수 T와 제네릭 메서드 sort()에 선언된 타입 매개변수 T는 타입 문자만 같을 뿐 서로 다른 것이다. 그리고 sort()가 static메서드라는 것에 주목해야 한다. 앞서 설명한 것처럼, static멤버에는 타입 매개변수를 사용할 수 없지만, 이처럼 메서드에 제네릭 타입을 선언하고 사용하는 것은 가능하다. 

메서드에 선언된 제네릭 타입은 지역 변수를 선언한 것과 같다고 생각하면 이해하기 쉬운데, 이 타입 매개변수는 메서드 내에서만 지역적으로 사용될 것이므로 메서드가 static이건 아니건 상관이 없다.

앞서 나왔던 makeJuice()를 제네릭 타입으로 바꾸면 다음과 같다.

```java
static <T extends Fruit> Juice makeJuice(FruitBox<T> box) {
  String tmp = "";
  for (Fruit f : box.getList()) tmp += f + " ";
  return new Juice(tmp);
}
```

이제 이 메서드를 호출할 때는 아래와 같이 타입 변수에 타입을 대입해야 한다.

```java
FruitBox<Fruit> fruitBox = new FruitBox<Fruit>();
FruitBox<Apple> appleBox = new FruitBox<Apple>();
    ...
System.out.println(Juicer.<Fruit>makeJuice(fruitBox));
System.out.println(Juicer.<Apple>makeJuice(appleBox));
```

그러나 대부분의 경우 컴파일러가 대입된 타입을 추정할 수 있기 때문에 생략해도 된다. 위의 코드에서도 fruitBox와 appleBox의 선언부를 통해 컴파일러가 대입된 타입을 추정할 수 있다.

```java
System.out.println(Juicer.makeJuice(fruitBox));   // 대입된 타입을 생략할 수 있다.
System.out.println(Juicer.makeJuice(appleBox));
```

한 가지 주의할 점은 제네릭 메서드를 호출할 때, 대입된 타입을 생략할 수 없는 경우에는 참조변수나 클래스 이름을 생략할 수 없다는 것이다.

```java
System.out.println(<Fruit>makeJuice(fruitBox));           // ERROR. 클래스 이름 생략 불가
System.out.println(this.<Fruit>makeJuice(fruitBox));      // OK
System.out.println(Juicer.<Fruit>makeJuice(fruitBox));    // OK
```

같은 클래스 내에 있는 멤버들끼리는 참조변수나 클래스이름, 즉 'this.'이나 '클래스이름.'을 생략하고 메서드 이름만으로 호출이 가능하지만, 대입된 타입이 있을 때는 반드시 써줘야 한다. 이것은 단지 기술적인 이유에 의한 규칙이므로 그냥 지키기만 하면 된다. 
