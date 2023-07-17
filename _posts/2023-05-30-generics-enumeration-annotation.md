---
layout: post
title: 제네릭스(Generics)
description: Java의 정석 기초편 ch. 12 지네릭스, 열거형, 애너테이션
categories: Java
date: 2023-05-30 12:07:00 +09:00
---
제네릭스(Generics)는 다양한 타입의 객체들을 다루는 메서드나 컬렉션 클래스에 컴파일 시의 타입 체크(compile-time type check)를 해주는 기능이다. 객체의 타입을 컴파일 시에 체크하기 때문에 객체의 타입 안정성을 높이고 형변환의 번거로움이 줄어든다. 

예를 들어 ArrayList의 경우 다양한 종류의 객체를 담을 수 있긴 하지만 보통 한 종류의 객체를 담는 경우가 많다. 아래와 같이 ArrayList를 생성할 때, 저장할 객체의 타입을 지정해주면, 지정한 타입 외에 다른 객체가 저장되면 에러가 발생한다.

```java
// Tv객체만 저장할 수 있는 ArrayList를 생성
ArrayList<Tv> tvList = new ArrayList<Tv>();

tvList.add(new Tv());       // OK
tvList.add(new Audio());    // 컴파일 에러. Tv 외에 다른 타입은 저장 불가
```

그리고 저장된 객체를 꺼낼 때는 형변환할 필요가 없어서 편리하다. 이미 어떤 타입의 객체들이 저장되어 있는지 알고 있기 때문이다. 제네릭스를 적용한 코드(보기 2)와 그렇지 않는 코드(보기 1)을 비교해보자.

```java
// 보기 1

ArrayList tvList = new ArrayList();
tvList.add(new Tv());
Tv t = (Tv)tvList.get(0);
```

```java
// 보기 2

ArrayList<Tv> tvList = new ArrayList<Tv>();
tvList.add(new Tv());
Tv t = tvList.get(0);   // 형변환 불필요 
```

정리하면, 제네릭스를 도입함으로써 얻는 장점은 다음과 같다.

> **제네릭스의 장점**
> 1. 타입 안정성을 제공한다.
> 2. 타입 체크와 형변환을 생략할 수 있으므로 코드가 간결해진다.

(타입 안정성을 높인다는 것은 의도하지 않은 타입의 객체를 저장하는 것을 막고, 저장된 객체를 꺼내올 때 원래의 타입과 다른 타입으로 형변환되어 발생할 수 있는 오류를 줄여준다는 말이다.)


## 타입 변수

ArrayList클래스의 선언에서 클래스 이름 옆의 '< >' 안에 있는 E를 '타입 변수(type variable)'라고 하며, 일반적으로는 'Type'의 첫 글자를 따서 T를 사용한다.

그렇다고 타입 변수로 반드시 T를 사용해야 하는 것은 아니며, T가 아닌 다른 것을 사용해도 된다. ArrayList\<E\>의 경우, 'Element(요소)'의 첫 글자를 따서 타입 변수의 이름으로 E를 사용한다.

```java
public class ArrayList<E> extends AbstractList<E> {
  private transient E[] elementData;
  public boolean add(E o) { /* 내용 생략 */ }
  public E get(int index) { /* 내용 생략 */ }
    ...
}
```

타입 변수가 여러 개인 경우에는 Map\<K, V\>와 같이 콤마 ','를 구분자로 나열하면 된다. K는 Key(키)를 의미하고, V는 Value(값)을 의미한다. 이들은 기호의 종류만 다를 뿐 '임의의 참조형 타입'을 의미한다는 것은 모두 같다. 마치 수학식 'f(x, y) = x + y'가 'f(k, v) = k + v'와 다르지 않은 것처럼 말이다.

아래의 코드는 제네릭스가 도입되기 이전의 ArrayList의 소스이다. 위의 코드와 비교해보면, Object타입 대신 임의의 타입을 의미하는 타입 변수 'E'가 사용된 것을 알 수 있다.

```java
public class ArrayList extends AbstractList {
  private transient Object[] elementData;
  public boolean add(Object o) { /* 내용 생략 */ }
  public Object get(int index) { /* 내용 생략 */ }
    ...
}
```

기존에는 다양한 종류의 타입을 다루는 메서드의 매개변수나 리턴타입으로 Object타입의 참조변수를 많이 사용했고, 그로 인해 형변환이 불가피했지만, 이제는 Object타입 대신 원하는 타입을 지정하기만 하면 되는 것이다.

```java
// 타입 변수 E 대신에 실제 타입 Tv를 대입
ArrayList<Tv> tvList = new ArrayList<Tv>();
```


## 타입 변수에 대입하기

ArrayList와 같은 제네릭 클래스를 생성할 때는 다음과 같이 참조변수와 생성자에 타입 변수 E 대신에 Tv와 같은 실제 타입을 지정해주어야 한다.

```java
// 타입 변수 E 대신에 실제 타입 Tv를 대입
ArrayList<Tv> tvList = new ArrayList<Tv>();
```

이때, 타입 변수 E 대신 지정된 타입 Tv를 '대입된 타입(parameterized type)'이라고 한다.

```java
public class ArrayList<E> extends AbstractList<E> {   // 일부 생략
  private transient E[] elementData;
  public boolean add(E o) { /* 내용 생략 */ }
  public E get(int index) { /* 내용 생략 */ }
    ...
}
```

타입이 대입되고 나면, ArrayList의 선언에 포함된 타입 변수 E가 아래와 같이 지정된 타입으로 바뀐다고 생각하면 된다.

```java
public class ArrayList extends AbstractList {   // 일부 생략
  private transient Tv[] elementData;
  public boolean add(Tv o) { /* 내용 생략 */ }
  public Tv get(int index) { /* 내용 생략 */ }    // Object가 아닌 Tv를 반환
    ...
}
```

위 코드의 get()이 Object가 아닌 Tv를 반환하게 되므로 형변환이 필요 없게 되는 것이다.

```java
// ArrayList tvList = new ArrayList();
// tvList.add(new Tv());
// Tv t = (Tv)tvList.get(0);

ArrayList<Tv> tvList = new ArrayList<Tv>();
tvList.add(new Tv());
Tv t = tvList.get(0);   // 형변환 불필요 
```


## 제네릭스의 용어

제네릭스에서 사용되는 용어들은 자칫 헷갈리기 쉽다. 다음과 같이 제네릭 클래스 Box가 선언되어 있을 때,

<img width="220" alt="[자바의 정석 - 기초편] ch12-4~6 지네릭스용어, 지네릭 타입과 다형성" title="[자바의 정석 - 기초편] ch12-4~6 지네릭스용어, 지네릭 타입과 다형성" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1160ade3-bf1d-4271-a9d7-de4dc591607e">

> **Box\<T\>** - 제네릭 클래스. 'T의 Box' 또는 'T Box'라고 읽는다.\
> **T** - 타입 변수 또는 타입 매개변수. (T는 타입 문자)\
> **Box** - 원시 타입(raw type)

타입 문자 T는 제네릭 클래스 Box\<T\>의 타입 변수 또는 타입 매개변수라고 하는데, 메서드의 매개변수와 유사한 면이 있기 때문이다. 그래서 아래와 같이 타입 매개변수에 타입을 지정하는 것을 '제네릭 타입 호출'이라고 하고, 지정된 타입 'String'을 '매개변수화된 타입(parameterized type)'이라고 한다. 매개변수화된 타입이라는 용어가 좀 길어서, 앞으로 이 용어 대신 '대입된 타입'이라는 용어를 사용할 것이다.

<img width="500" alt="[자바의 정석 - 기초편] ch12-4~6 지네릭스용어, 지네릭 타입과 다형성" title="[자바의 정석 - 기초편] ch12-4~6 지네릭스용어, 지네릭 타입과 다형성" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0c210b3b-e321-40fb-9998-b9129fd5ac4c">

예를 들어, Box\<String\>과 Box\<Integer\>는 제네릭 클래스 Box\<T\>에 서로 다른 타입을 대입하여 호출한 것일 뿐, 이 둘이 별개의 클래스를 의미하는 것은 아니다. 이는 마치 매개변수의 값이 다른 메서드 호출, 즉 add(3, 5)와 add(2, 4)가 서로 다른 메서드를 호출하는 것이 아닌 것과 같다.

**컴파일 후에 Box\<String\>과 Box\<Integer\>는 이들의 '원시 타입'인 Box로 바뀐다. 즉, 제네릭 타입이 제거된다.**


## 제네릭 타입과 다형성

제네릭 클래스의 객체를 생성할 때, 참조변수에 지정해준 제네릭 타입과 생성자에 지정해준 제네릭 타입은 일치해야 한다. 클래스 Tv와 Product가 서로 상속관계에 있어도 일치해야 한다.

```java
ArrayList<Tv> list = new ArrayList<Tv>();        // OK. 일치
ArrayList<Product> list = new ArrayList<Tv>();   // ERROR. 불일치
  ...
class Product { }
class Tv extends Product { }
class Audio extends Product { }
```

그러나 제네릭 타입이 아닌 클래스의 타입 간에 다형성을 적용하는 것은 가능하다. 이 경우에도 제네릭 타입은 일치해야 한다.

```java
List<Tv> list = new ArrayList<Tv>();    // OK. 다형성. ArrayList가 List를 구현
List<Tv> list = new LinkedList<Tv>();   // OK. 다형성. LinkedList가 List를 구현
```

그러면 ArrayList에 Product의 자손 객체만 저장할 수는 없을까? 그럴 때는 제네릭 타입이 Product인 ArrayList를 생성하고, 이 ArrayList에 Product의 자손인 Tv와 Audio의 객체를 저장하면 된다.

```java
ArrayList<Product> list = new ArrayList<Product>();
list.add(new Product());
list.add(new Tv());       // OK.
list.add(new Audio());    // OK.
```

대신 ArrayList에 저장된 객체를 꺼낼 때, 형변환이 필요하다.

```java
Product p = list.get(0);    // Product객체는 형변환이 필요없다.
Tv t = (Tv)list.get(1);     // Product의 자손 객체들은 형변환을 필요로 한다.
```


## 제네릭 타입과 다형성 예제

```java
import java.util.*;

class Product {}
class Tv extends Product {}
class Audio extends Product {}

class Ex12_1 {
	public static void main(String[] args) {
		ArrayList<Product> productList = new ArrayList<Product>();
		ArrayList<Tv>      tvList = new ArrayList<Tv>();
//              ArrayList<Product> tvList = new ArrayList<Tv>();  // 에러.
//              List<Tv>           tvList = new ArrayList<Tv>();  // OK. 다형성 

		productList.add(new Tv());
		productList.add(new Audio());

		tvList.add(new Tv());
		tvList.add(new Tv());

		printAll(productList);
		// printAll(tvList);  // 컴파일 에러가 발생한다. 
	}

	public static void printAll(ArrayList<Product> list) {
		for (Product p : list)
			System.out.println(p);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Tv@4678c730
Audio@6767c1fc
```
