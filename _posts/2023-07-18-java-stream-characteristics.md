---
layout: post
title: 스트림(stream)
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-18 14:41:00 +0900
---
지금까지 우리는 많은 수의 데이터를 다룰 때, 컬렉션이나 배열에 데이터를 담고 원하는 결과를 얻기 위해 for문과 Iterator를 이용해서 코드를 작성해왔다. 그러나 이러한 방식으로 작성된 코드는 너무 길고 알아보기 어렵다. 그리고 재사용성도 떨어진다.

또 다른 문제는 데이터 소스마다 다른 방식으로 다뤄야 한다는 것이다. Collection이나 Iterator와 같은 인터페이스를 이용해서 컬렉션을 다루는 방식을 표준화하기는 했지만, 각 컬렉션 클래스에는 같은 기능의 메서드들이 중복해서 정의되어 있다. 예를 들어 List를 정렬할 때는 Collections.sort()를 사용해야 하고, 배열을 정리할 때는 Arrays.sort()를 사용해야 한다.

이러한 문제점들을 해결하기 위해서 만든 것이 '스트림(Stream)'이다. 스트림은 데이터 소스를 추상화하고, 데이터를 다루는 데 자주 사용되는 메서드들을 정의해 놓았다. 데이터 소스를 추상화하였다는 것은, 데이터 소스가 무엇이든 간에 같은 방식으로 다룰 수 있게 되었다는 것과 코드의 재사용성이 높아진다는 것을 의미한다.

스트림을 이용하면, 배열이나 컬렉션뿐만 아니라 파일에 저장된 데이터도 모두 같은 방식으로 다룰 수 있다.

예를 들어, 문자열 배열과 같은 내용의 문자열을 저장하는 List가 있을 때,

```java
String[] strArr = { "aaa", "ddd", "ccc" };
List<String> strList = Arrays.asList(strArr);
```

이 두 데이터 소스를 기반으로 하는 스트림은 다음과 같이 생성한다.

```java
Stream<String> strStream1 = strList.stream();           // 스트림을 생성
Stream<String> strStream2 = Arrays.stream(strArr);      // 스트림을 생성
```

이 두 스트림으로 데이터 소스의 데이터를 읽어서 정렬하고 화면에 출력하는 방법은 다음과 같다. 데이터 소스가 정렬되는 것이 아니라는 것에 유의하자.

```java
strStream1.sorted().forEach(System.out::println);
strStream2.sorted().forEach(System.out::println);
```

두 스트림의 데이터 소스는 서로 다르지만, 정렬하고 출력하는 방법은 완전히 동일하다.


## 스트림의 특징

### 스트림은 데이터 소스를 변경하지 않는다.

그리고 스트림은 데이터 소스로부터 데이터를 읽기만 할 뿐, 데이터 소스를 변경하지 않는다는 차이가 있다. 필요하다면, 정렬된 결과를 컬렉션이나 배열에 담아서 반환할 수도 있다.

```java
// 정렬된 결과를 새로운 List에 담아서 반환한다.
List<String> sortedList = strStream2.sorted().collect(Collectors.toList());
```


### 스트림은 일회용이다.

스트림은 Iterator처럼 일회용이다. Iterator로 컬렉션의 요소를 모두 읽고 나면 다시 사용할 수 없는 것처럼, 스트림도 한 번 사용하면 닫혀서 다시 사용할 수 없다. 필요하다면 스트림을 다시 생성해야 한다.

```java
strStream1.sorted().forEach(System.out::println);
int numOfStr = strStream1.count();      // ERROR. 스트림이 이미 닫혔음.
```


### 스트림은 작업을 내부 반복으로 처리한다.

스트림을 이용한 작업이 간결할 수 있는 비결 중의 하나가 바로 '내부 반복'이다 내부 반복이라는 것은 반복문을 메서드의 내부에 숨겼다는 것을 의미한다. forEach()는 스트림에 정의된 메서드 중의 하나로 매개변수에 대입된 람다식을 데이터 소스의 모든 요소에 적용한다.

```java
for (String str : strList)
    System.out.println(str);
```

↓

```java
stream.forEach(System.out::println);
```

(메서드 참조 System.out::println를 람다식으로 표현하면 (str) -> System.out.println(str)과 같다.)

즉, forEach()는 메서드 안으로 for문을 넣은 것이다. 수행할 작업은 매개변수로 받는다.

```java
void forEach(Consumer<? super T> action) {
    Objects.requireNonNull(action);     // 매개변수의 null 체크

    for (T t : src) {       // 내부 반복
        action.accept(T);
    }
}
```


### 지연된 연산

스트림 연산에서 한 가지 중요한 점은 최종 연산이 수행되기 전까지는 중간 연산이 수행되지 않는다는 것이다. 스트림에 대해 distinct()나 sort() 같은 중간 연산을 호출해도 즉각적인 연산이 수행되지 않는다. 중간 연산을 호출하는 것은 단지 어떤 작업이 수행되어야 하는지를 지정해주는 것일 뿐이다. 최종 연산이 수행되어야 비로소 스트림의 요소들이 중간 연산을 거쳐 최종 연산에서 소모된다.


### Stream<Integer>와 IntStream

요소의 타입이 T인 스트림은 기본적으로 Stream\<T\>이지만, 오토박싱&언박싱으로 인한 비효율을 줄이기 위해 데이터 소스의 요소를 기본형으로 다루는 스트림, IntStream, LongStream, DoubleStream이 제공된다. 일반적으로 Stream<Integer> 대신 IntStream을 사용하는 것이 더 효율적이고, IntStream에는 int타입의 값으로 작업하는 데 유용한 메서드들이 포함되어 있다.


### 병렬 스트림

스트림으로 데이터를 다룰 때의 장점 중 하나가 바로 병렬 처리가 쉽다는 것이다. 병렬 스트림은 내부적으로 Java에서 제공하는 fork & join 프레임워크를 이용해서 자동적으로 연산을 병렬로 수행한다. 우리가 할 일이라고는 그저 스트림에 parallel()이라는 메서드를 호출해서 병렬로 연산을 수행하도록 지시하면 될 뿐이다. 반대로 병렬로 처리되지 않게 하려면 sequential()을 호출하면 된다. 모든 스트림은 기본적으로 병렬 스트림이 아니므로 sequential()을 호출할 필요가 없다. 이 메서드는 parallel()을 호출한 것을 취소할 때만 사용한다.

(parallel()과 sequential()은 새로운 스트림을 생성하는 것이 아니라, 그저 스트림의 속성을 변경할 뿐이다.)

```java
int sum = strStream.parrellel()     // strStream을 병렬 스트림으로 전환
                    .mapToInt(s -> s.length())
                    .sum();
```