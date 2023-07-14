---
layout: post
title: java.util.function패키지
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-14 11:39:00 +0900
---
대부분의 메서드는 타입이 비슷하다. 매개변수가 없거나 한 개 또는 두 개, 반환 값은 없거나 한 개. 게다가 제네릭 메서드로 정의하면 매개변수나 반환 타입이 달라도 문제가 되지 않는다. 그래서 java.util.function패키지에 일반적으로 자주 쓰이는 형식의 메서드를 함수형 인터페이스로 미리 정의해 놓았다. 매번 새로운 함수형 인터페이스를 정의하지 말고, 가능하면 이 패키지의 인터페이스를 활용하는 것이 좋다.

그래야 함수형 인터페이스에 정의된 메서드 이름도 통일되고, 재사용성이나 유지보수 측면에서도 좋다. 자주 쓰이는 가장 기본적인 함수형 인터페이스는 다음과 같다.

<img width="750" alt="[자바의 정석 - 기초편] ch14-7,8 java.util.function패키지" title="[자바의 정석 - 기초편] ch14-7,8 java.util.function패키지" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/d2e73d5f-5ba9-440c-83ab-b42ec6dbe8da">

매개변수와 반환값의 유무에 따라 4개의 함수형 인터페이스가 정의되어 있고, Function의 변형으로 Predicate가 있는데, 반환값이 boolean이라는 것만 제외하면 Function과 동일하다. Predicate는 조건식을 함수로 표현하는 데 사용된다.

(타입 문자 'T'는 'Type'를, 'R'은 'Return Type'을 의미한다.)

```java
Predicate<String> isEmptyStr = s -> s.length() == 0;
String s = "";

if (isEmptyStr.test(s))     // if (s.length() == 0)
    System.out.println("This is an empty String");
```


## 매개변수가 두 개인 함수형 인터페이스

매개변수의 개수가 2개인 함수형 인터페이스는 이름 앞에 접두사 'Bi'가 붙는다.

(매개변수의 타입으로 보통 'T'를 사용하므로, 알파벳에서 'T'의 다음 문자인 'U', 'V', 'W'를 매개변수의 타입으로 사용하는 것일 뿐 별다른 의미는 없다.)

<img width="750" alt="[자바의 정석 - 기초편] ch14-7,8 java.util.function패키지" title="[자바의 정석 - 기초편] ch14-7,8 java.util.function패키지" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ef60935b-7e6f-4370-a278-d60a7512f9a7">

(Supplier는 매개변수는 없고 반환값만 존재하는데, 메서드는 두 개의 값을 반환할 수 없으므로 BiSupplier가 없는 것이다.)

두 개 이상의 매개변수를 갖는 함수형 인터페이스가 필요하다면 직접 만들어서 써야 한다. 만일 3개의 매개변수를 갖는 함수형 인터페이스를 선언한다면 다음과 같을 것이다.

```java
@FunctionalInterface
interface TriFunction<T, U, V, R> {
    R apply(T t, U u, V v);
}
```


## UnaryOperator와 BinaryOperator

Function의 또 다른 변형으로 UnaryOperator와 BinaryOperator가 있는데, 매개변수의 타입과 반환타입의 타입이 모두 일치한다는 점만 제외하고는 Function과 같다.

(UnaryOperator와 BinaryOperator의 조상은 각각 Function과 BiFunction이다.)

<img width="750" alt="[자바의 정석 - 기초편] ch14-7,8 java.util.function패키지" title="[자바의 정석 - 기초편] ch14-7,8 java.util.function패키지" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/742686ef-e465-467c-bed5-84da35188074">


## java.util.function패키지 예제

```java
import java.util.function.*;
import java.util.*;

class Ex14_2 {
	public static void main(String[] args) {
		Supplier<Integer>  s = ()-> (int)(Math.random()*100)+1;
		Consumer<Integer>  c = i -> System.out.print(i+", "); 
		Predicate<Integer> p = i -> i%2==0; 
		Function<Integer, Integer> f = i -> i/10*10; // i의 일의 자리를 없앤다. 
		
		List<Integer> list = new ArrayList<>();	
		makeRandomList(s, list);
		System.out.println(list);
		printEvenNum(p, c, list);
		List<Integer> newList = doSomething(f, list);
		System.out.println(newList);
	}

	static <T> List<T> doSomething(Function<T, T> f, List<T> list) {
		List<T> newList = new ArrayList<T>(list.size());

		for(T i : list) {
			newList.add(f.apply(i));
		}	

		return newList;
	}

	static <T> void printEvenNum(Predicate<T> p, Consumer<T> c, List<T> list) {
		System.out.print("[");
		for(T i : list) {
			if(p.test(i))
				c.accept(i);
		}	
		System.out.println("]");
	}

	static <T> void makeRandomList(Supplier<T> s, List<T> list) {
		for(int i=0;i<10;i++) {
			list.add(s.get());
		}
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
[19, 30, 3, 66, 59, 96, 1, 28, 45, 94]
[30, 66, 96, 28, 94, ]
[10, 30, 0, 60, 50, 90, 0, 20, 40, 90]
```