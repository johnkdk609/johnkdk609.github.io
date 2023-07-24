---
layout: post
title: 스트림의 최종연산
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-24 22:40:00 +0900
---
최종 연산은 스트림의 요소를 소모해서 결과를 만들어낸다. 그래서 최종 연산 후에 스트림은 닫히게 되고 더 이상 사용할 수 없다. 최종 연산의 결과는 스트림 요소의 합과 같은 단일 값이거나, 스트림의 요소가 담긴 배열 또는 컬렉션일 수 있다.

forEach()나 count() 같은 최종 연산의 일부는 이미 배웠으며, 나머지도 별로 어렵지 않다. 다만, collect()는 별도로 다뤄야 할 정도로 복잡하다.


## 스트림의 최종연산 - forEach()

forEach()는 peek()과 달리 스트림의 요소를 소모하는 최종연산이다. 반환 타입이 void이므로 스트림의 요소를 출력하는 용도로 많이 사용된다.

```java
void forEach(Consumer<? super T> action)
```

지금까지 자주 사용해왔기 때문에 충분히 익숙할 것이라 생각하고 자세한 설명은 생략한다.


## 스트림의 최종연산 - 조건검사

스트림의 요소에 대해 지정된 조건에 모든 요소가 일치하는지, 일부가 일치하는지 아니면 어떤 요소도 일치하지 않는지 확인하는 데 사용할 수 있는 메서드들이다. 이 메서드들은 모두 매개변수로 Predicate를 요구하며, 연산 결과로 boolean을 반환한다.

```java
boolean allMatch (Predicate<? super T> predicate)       // 모든 요소가 일치하면 참
boolean anyMatch (Predicate<? super T> predicate)       // 하나의 요소라도 일치하면 참
boolean noneMatch(Predicate<? super T> predicate)       // 모든 요소가 불일치하면 참
```

예를 들어 학생들의 성적 정보 스트림 stuStream에서 총점이 낙제점(총점 100 이하)인 학생이 있는지 확인하는 방법은 다음과 같다.

```java
boolean noFailed = stuStream.anyMatch(s -> s.getTatalScore() <= 100)
```

이외에도 스트림의 요소 중에서 조건에 일치하는 첫 번째 것을 반환하는 findFirst()가 있는데, 주로 filter()와 함께 사용되어 조건에 맞는 스트림의 요소가 있는지 확인하는 데 사용된다. 병렬 스트림인 경우에는 findFirst() 대신 findAny()를 사용해야 한다.

```java
Optional<Student> stu = stuStream.filter(s -> s.getTotalScore() <= 100).findFirst();
Optional<Student> stu = parallelStream.filter(s -> s.getTotalScore() <= 100).findAny();
```

findAny()와 findFirst()의 반환 타입은 Optional&#60;T&#62;이며, 스트림의 요소가 없을 때는 비어있는 Optional객체를 반환한다.

(비어있는 Optional객체는 내부적으로 null을 저장하고 있다.)

```java
Optional<T> findFirst()     // 조건에 일치하는 첫 번째 요소를 반환
Optional<T> findAny()       // 조건에 일치하는 요소를 하나 반환(병렬 스트림)
```


## 스트림의 최종연산 - reduce()

reduce()는 이름에서 짐작할 수 있듯이, 스트림의 요소를 줄여 나가면서 연산을 수행하고 최종결과를 반환한다. 그래서 매개변수의 타입이 BinaryOperator&#60;T&#62;인 것이다. 처음 두 요소를 가지고 연산한 결과를 가지고 그 다음 요소와 연산한다.

이 과정에서 스트림의 요소를 하나씩 소모하며, 스트림의 모든 요소를 소모하게 되면 그 결과를 반환한다.

```java
Optional<T> reduce(BinaryOperator<T> accumulator)
```

이외에도 연산 결과의 초깃값(identity)을 갖는 reduce()도 있는데, 이 메서드들은 초깃값과 스트림의 첫 번째 요소로 연산을 시작한다. 스트림의 요소가 하나도 없는 경우, 초깃값이 반환되므로, 반환 타입이 Optional&#60;T&#62;가 아니라 T이다.

(BinaryOperator&#60;T&#62;는 BiFunction의 자손이며, BiFunction&#60;T, T, T&#62;와 동등하다.)

```java
T reduce(T identity, BinaryOperator<T> accumulator)
U reduce(U identity, BiFunction<U, T, U> accumulator, BinaryOperator<U> combiner)
```

위의 두 번째 메서드의 마지막 매개변수인 combiner는 병렬 스트림에 의해 처리된 결과를 합칠 때 사용하기 위해 사용하는 것이다.

앞서 소개한 최종 연산 count()와 sum() 등은 내부적으로 모두 reduce()를 이용해서 아래와 같이 작성된 것이다.

```java
int count = intStream.reduce(0, (a, b) -> a + 1);                           // count()
int sum = intStream.reduce(0, (a, b) -> a + b);                             // sum()
int max = intStream.reduce(Integer.MIN_VALUE, (a, b) -> a > b ? a : b);     // max()
int min = intStream.reduce(Integer.MAX_VALUE, (a, b) -> a < b ? a : b);     // min()
```


## 스트림의 최종연산 - reduce()의 이해

reduce()가 내부적으로 어떻게 동작하는지 이해를 돕기 위해, reduce()로 스트림의 모든 요소를 다 더하는 과정을 for문으로 표현해 보았다.

```java
int a = identity;       // 초깃값을 a에 저장한다.

for (int b : stream)
    a = a + b;          // 모든 요소의 값을 a에 누적한다.
```

위의 for문을 보고 나면, reduce()가 아마도 다음과 같이 작성되어 있을 것이라고 추측하는 것은 그리 어려운 일이 아닐 것이다.

```java
T reduce(T identity, BinaryOperator<T> accumulator) {
    T a = identity;

    for (T b : stream)
        a = accumulator.apply(a, b);

    return a;
}
```

reduce()를 사용하는 방법은 간단하다. 그저 초깃값(identity)과 어떤 연산(Binary Operator)으로 스트림의 요소를 줄여나갈 것인지만 결정하면 된다.


## 스트림의 최종연산 - reduce() 예제

```java
import java.util.*;
import java.util.stream.*;

class Ex14_9 {
	public static void main(String[] args) {
		String[] strArr = {
			"Inheritance", "Java", "Lambda", "stream",
			"OptionalDouble", "IntStream", "count", "sum"
		};

		Stream.of(strArr).forEach(System.out::println);

		boolean noEmptyStr = Stream.of(strArr).noneMatch(s->s.length()==0);
		Optional<String> sWord = Stream.of(strArr)
					               .filter(s->s.charAt(0)=='s').findFirst();

		System.out.println("noEmptyStr="+noEmptyStr);
		System.out.println("sWord="+ sWord.get());

		// Stream<String>을 IntStream으로 변환 
		IntStream intStream1 = Stream.of(strArr).mapToInt(String::length);
		IntStream intStream2 = Stream.of(strArr).mapToInt(String::length);
		IntStream intStream3 = Stream.of(strArr).mapToInt(String::length);
		IntStream intStream4 = Stream.of(strArr).mapToInt(String::length);

		int count = intStream1.reduce(0, (a,b) -> a + 1);
		int sum   = intStream2.reduce(0, (a,b) -> a + b);

		OptionalInt max = intStream3.reduce(Integer::max);
		OptionalInt min = intStream4.reduce(Integer::min);
		System.out.println("count="+count);
		System.out.println("sum="+sum);
		System.out.println("max="+ max.getAsInt());
		System.out.println("min="+ min.getAsInt());
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Inheritance
Java
Lambda
stream
OptionalDouble
IntStream
count
sum
noEmptyStr=true
sWord=stream
count=8
sum=58
max=14
min=3
```