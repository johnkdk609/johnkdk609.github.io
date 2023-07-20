---
layout: post
title: 스트림의 연산
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-20 22:34:00 +0900
---
스트림이 제공하는 다양한 연산을 이용하면 복잡한 작업들을 간단히 처리할 수 있다. 마치 데이터베이스에 SELECT문으로 질의(쿼리, query)하는 것과 같은 느낌이다.

(스트림에 정의된 메서드 중에서 데이터 소스를 다루는 작업을 수행하는 것을 연산(operation)이라고 한다.)

스트림이 제공하는 연산은 중간 연산과 최종 연산으로 분류할 수 있는데, 중간 연산은 연산 결과를 스트림으로 반환하기 때문에 중간 연산을 연속해서 연결할 수 있다. 반면에 최종 연산은 스트림의 요소를 소모하면서 연산을 수행하므로 단 한 번만 연산이 가능하다.

> 중간 연산 - 연산 결과가 스트림인 연산. 스트림에 연속해서 중간 연산할 수 있음
> 최종 연산 - 연산 결과가 스트림이 아닌 연산. 스트림의 요소를 소모하므로 단 한 번만 가능

```java
stream.distinct().limit(5).sorted().forEach(System.out::println)
//      중간 연산    중간 연산  중간 연산           최종 연산
```

모든 중간 연산의 결과는 스트림이지만, 연산 전의 스트림과 같은 것은 아니다. 위의 문장과 달리 모든 스트림 연산을 나누어 쓰면 아래와 같다. 각 연산의 반환 타입을 눈여겨 봐야 한다.

```java
String[] strArr = { "dd", "aaa", "CC", "cc", "b" };
Stream<String> stream = Stream.of(strArr);              // 문자열 배열이 소스인 스트림
Stream<String> filteredStream = stream.filter();        // 걸러내기(중간 연산)
Stream<String> distinctedStream = stream.distinct();    // 중복 제거(중간 연산)
Stream<String> sortedStream = stream.sort();            // 정렬(중간 연산)
Stream<String> limitedStream = stream.limit(5);         // 스트림 자르기(중간 연산)
int total = stream.count();                             // 요소 개수 세기(최종 연산)
```


## 스트림의 연산 - 중간 연산

Stream에 정의된 중간 연산을 정리하면 다음과 같다.

<table>
    <tr>
        <th colspan="2">중간 연산</th>
        <th>설명</th>
    </tr>
    <tr>
        <td colspan="2">Stream&#60;T&#62; distinct()</td>
        <td>중복을 제거</td>
    </tr>
    <tr>
        <td colspan="2">Stream&#60;T&#62; filter(Predicate&#60;T&#62; predicate)</td>
        <td>조건에 안 맞는 요소 제외</td>
    </tr>
    <tr>
        <td colspan="2">Stream&#60;T&#62; limit(long maxSize)</td>
        <td>스트림의 일부를 잘라낸다.</td>
    </tr>
    <tr>
        <td colspan="2">Stream&#60;T&#62; skip(long n)</td>
        <td>스트림의 일부를 건너뛴다.</td>
    </tr>
    <tr>
        <td colspan="2">Stream&#60;T&#62; peek(Consumer&#60;T&#62; action)</td>
        <td>스트림의 요소에 작업 수행</td>
    </tr>
    <tr>
        <td colspan="2">Stream&#60;T&#62; sorted()<br>Stream&#60;T&#62; sorted(Comparator&#60;T&#62; comparator)</td>
        <td>스트림의 요소를 정렬한다.</td>
    </tr>
    <tr>
        <td>Stream&#60;R&#62;<br>DoubleStream<br>IntStream<br>LongStream<br><br>Stream&#60;R&#62;<br>DoubleStream<br>IntStream<br>LongStream</td>
        <td>map(Function&#60;T, R&#62; mapper)<br>mapToDouble(ToDoubleFunction&#60;T&#62; mapper)<br>mapToInt(ToIntFunction&#60;T&#62; mapper)<br>mapToLong(ToLongFunction&#60;T&#62; mapper)<br><br>flatMap(Function&#60;T, Stream&#60;R&#62;&#62; mapper)<br>flatMapToDouble(Function&#60;T, DoubleStream&#62; m)<br>flatMapToInt(Function&#60;T, IntStream&#62; m)<br>flatMapToLong(Function&#60;T, LongStream&#62; m)</td>
        <td>스트림의 요소를 반환한다.</td>
    </tr>
</table>

중간 연산은 map()과 flatMap()이 핵심이다. 나머지는 이해하기 쉽고 사용법도 간단하다.


## 스트림의 연산 - 최종 연산

Stream에 정의된 최종 연산을 정리하면 다음과 같다.

<table>
    <tr>
        <th>최종 연산</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>void forEach(Consumer&#60;? super T&#62; action)<br>void forEachOrdered(Consumer&#60;? super T&#62; action)</td>
        <td>각 요소에 지정된 작업 수행</td>
    </tr>
    <tr>
        <td>long count()</td>
        <td>스트림의 요소의 개수 반환</td>
    </tr>
    <tr>
        <td>Optional&#60;T&#62; max(Comparator&#60;? super T&#62; comparator)<br>Optional&#60;T&#62; min(Comparator&#60;? super T&#62; comparator)</td>
        <td>스트림의 최댓값/최솟값을 반환</td>
    </tr>
    <tr>
        <td>Optional&#60;T&#62; findAny()   // 아무거나 하나<br>Optional&#60;T&#62; findFirst() // 첫 번째 요소</td>
        <td>스트림의 요소 하나를 반환</td>
    </tr>
    <tr>
        <td>boolean allMatch(Predicate&#60;T&#62; p)    // 모두 만족하는지<br>boolean anyMatch(Predicate&#60;T&#62; p)  // 하나라도 만족하는지<br>boolean noneMatch(Predicate&#60;T&#62; p) // 모두 만족하지 않는지</td>
        <td>주어진 조건을 모든 요소가 만족시키는지, 만족시키지 않는지 확인</td>
    </tr>
    <tr>
        <td>Object[] toArray()<br>A[] toArray(IntFunction&#60;A[]&#62; generator)</td>
        <td>스트림의 모든 요소를 배열로 변환</td>
    </tr>
    <tr>
        <td>Optional&#60;T&#62; reduce(BinaryOperator&#60;T&#62; accumulator)<br>T reduce(T identity, BinaryOperator&#60;T&#62; accumulator)<br>U reduce(U identity, BiFunction&#60;U, T, U&#62; accumulator, BinaryOperator&#60;U&#62; combiner)</td>
        <td>스트림의 요소를 하나씩 줄여가면서(리듀싱) 계산한다.</td>
    </tr>
    <tr>
        <td>R collect(Collector&#60;T, A, R&#62; collector)<br>R collect(Supplier&#60;R&#62; supplier, BiConsumer&#60;R, T&#62; accumulator, BiConsumer&#60;R, R&#62; combiner)</td>
        <td>스트림의 요소를 수집한다.<br>주로 요소를 그룹화하거나 분할한 결과를 컬렉션에 담아 반환하는 데 사용된다.</td>
    </tr>
</table>

최종 연산은 reduce()와 collect()가 핵심이다. 나머지는 이해하기 쉽고 사용법도 간단하다.