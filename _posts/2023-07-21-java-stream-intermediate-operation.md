---
layout: post
title: 스트림의 중간연산
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-21 22:35:00 +0900
---
스트림의 중간 연산(intermediate operation)에 대해 자세히 알아보겠다.

## 스트림의 중간연산 - skip(), limit()

skip()과 limit()은 스트림의 일부를 잘라낼 때 사용하며, 사용법은 아주 간단하다. skip(3)은 처음 3개의 요소를 건너뛰고, limit(5)는 스트림의 요소를 5개로 제한한다.

```java
Stream<T> skip(long n)
Stream<T> limit(long maxSize)
```

예를 들어 10개의 요소를 가진 스트림에 skip(3)과 limit(5)을 순서대로 적용하면 4번째 요소부터 5개의 요소를 가진 스트림이 반환된다.

```java
IntStream intStream = IntStream.rangeClosed(1, 10);         // 1~10의 요소를 가진 스트림
intStream.skip(3).limit(5).forEach(System.out::println);    // 45678
```

기본형 스트림에도 skip()과 limit()이 정의되어 있는데, 반환 타입이 기본형 스트림이라는 점만 다르다.

```java
IntStream skip(long n)
IntStream limit(long maxSize)
```


## 스트림의 중간연산 - filter(), distinct()

distinct()는 스트림에서 중복된 요소들을 제거하고, filter()는 주어진 조건(Predicate)에 맞지 않는 요소를 걸러낸다.

```java
Stream<T> filter(Predicate<? super T> predicate)
Stream<T> distinct()
```

distinct()의 사용 방법은 간단하다.

```java
IntStream intStream = IntStream.of(1, 2, 2, 3, 3, 3, 4, 5, 5, 6);
intStream.distinct().forEach(System.out::print);    // 123456
```

filter()는 매개변수로 Predicate를 필요로 하는데, 아래와 같이 연산 결과가 boolean인 람다식을 사용해도 된다.

```java
IntStream intStream = IntStream.rangeClosed(1, 10);     // 1~10
intStream.filter(i -> i % 2 == 0).forEach(System.out::print);   // 246810
```

필요하다면 filter()를 다른 조건으로 여러 번 사용할 수도 있다.

```java
// 아래의 두 문장은 동일한 결과를 얻는다.
intStream.filter(i -> i % 2 != 0 && i % 3 != 0).forEach(System.out::print);     // 157
intStream.filter(i -> i % 2 != 0).filter(i -> i % 3 != 0).forEach(System.out::print);
```


## 스트림의 중간연산 - sorted()

스트림을 정렬할 때는 sorted()를 사용하면 된다.

```java
Stream<T> sorted()
Stream<T> sorted(Comparator<? super T> comparator)
```

sorted()는 지정된 Comparator로 스트림을 정렬하는데, Comparator 대신 int값을 반환하는 람다식을 사용하는 것도 가능하다. Comparator를 지정하지 않으면 스트림 요소의 기본 정렬 기준(Comparable)으로 정렬한다. 단, 스트림의 요소가 Comparable을 구현한 클래스가 아니면 예외가 발생한다.

```java
Stream<String> strStream = Stream.of("dd", "aaa", "CC", "cc", "b");
strStream.sorted().forEach(System.out::print);      // CCaaabccdd
```

위의 코드는 문자열 스트림을 String에 정의된 기본 정렬(사전순 정렬)로 정렬해서 출력한다. 아래의 표는 위의 문자열 스트림(strStream)을 다양한 방법으로 정렬한 후에 forEach(System.out::print)로 출력한 결과를 보여준다.

(String.CASE_INSENSITVIE_ORDER는 String클래스에 정의된 Comparator이다.)

<table>
    <tr>
        <th>문자열 스트림 정렬 방법</th>
        <th>출력 결과</th>
    </tr>
    <tr>
        <td>strStream.sorted()  // 기본 정렬<br>strStream.sorted(Comparator.naturalOrder()) // 기본 정렬<br>strStream.sorted((s1, s2) -> s1.compareTo(s2));  // 람다식도 가능<br>strStream.sorted(String::compareTo);   // 위의 문장과 동일</td>
        <td>CCaaabccdd</td>
    </tr>
    <tr>
        <td>strStream.sorted(Comparator.reverseOrder()) // 기본 정렬의 역순<br>strStream.sorted(Comparator.&#60;String&#62;naturalOrder().reversed())</td>
        <td>ddccbaaaCC</td>
    </tr>
    <tr>
        <td>strStream.sorted(String.CASE_INSENSITIVE_ORDER) // 대소문자 구분 안 함</td>
        <td>aaabCCccdd</td>
    </tr>
    <tr>
        <td>strStream.sorted(String.CASE_INSENSITIVE_ORDER.reversed())</td>
        <td>ddCCccbaaa (오타 아님)</td>
    </tr>
    <tr>
        <td>strStream.sorted(Comparator.comparing(String::length))  // 길이 순 정렬<br>strStream.sorted(Comparator.comparingInt(String::length))    // no오토박싱</td>
        <td>bddCCccaaa</td>
    </tr>
    <tr>
        <td>strStream.sorted(Comparator.comparing(String::length).reversed())</td>
        <td>aaaddCCccb</td>
    </tr>
</table>


## 스트림의 중간연산 - Comparator의 메서드

JDK1.8부터 Comparator인터페이스에 static메서드와 디폴트 메서드가 많이 추가되었는데, 이 메서드들을 이용하면 정렬이 쉬워진다. 이 메서드들은 모두 Comparator&#60;T&#62;를 반환하며, 가장 기본적인 메서드는 comparing()이다.

```java
comparing(Function<T, U> keyExtractor)
comparing(Function<T, U> keyExtractor, Comparator<U> keyComparator)
```

스트림의 요소가 Comparable을 구현한 경우, 매개변수 하나짜리를 사용하면 되고 그렇지 않은 경우, 추가적인 매개변수로 정렬기준(Comparator)을 따로 지정해 줘야 한다.

```java
comparingInt(ToIntFunction<T> keyExtractor)
comparingLong(ToLongFunction<T> keyExtractor)
comparingDouble(ToDoubleFunction<T> keyExtractor)
```

비교대상이 기본형인 경우, comparing() 대신 위의 메서드를 사용하면 오토박싱과 언박싱 과정이 없어서 더 효율적이다. 그리고 정렬 조건을 추가할 때는 thenComparing()을 사용한다.

```java
thenComparing(Comparator<T> other)
thenComparing(Function<T, U> keyExtractor)
thenComparing(Function<T, U> keyExtractor, Comparator<U> keyComp)
```

예를 들어 학생 스트림(studentStream)을 반(ban)별, 성적(totalScore)순, 그리고 이름(name)순으로 정렬하여 출력하려면 다음과 같이 한다.

```java
studentStream.sorted(Comparator.comparing(Student::getBan))
                            .thenComparing(Student::getTotalScore)
                            .thenComparing(Student::getName)
                            .forEach(System.out::println)
```

아래는 스트림의 중간연산 - Comparator메서드의 예제 코드이다.

```java
import java.util.*;
import java.util.stream.*;

class Ex14_5 {
	public static void main(String[] args) {
		Stream<Student> studentStream = Stream.of(
						new Student("이자바", 3, 300),
						new Student("김자바", 1, 200),
						new Student("안자바", 2, 100),
						new Student("박자바", 2, 150),
						new Student("소자바", 1, 200),
						new Student("나자바", 3, 290),
						new Student("감자바", 3, 180)
					);

		studentStream.sorted(Comparator.comparing(Student::getBan) // 반별 정렬 
		 	.thenComparing(Comparator.naturalOrder()))     	// 기본 정렬 
			.forEach(System.out::println);
	}
}

class Student implements Comparable<Student> {
	String name;
	int ban;
	int totalScore;
	Student(String name, int ban, int totalScore) { 
		this.name =name;
		this.ban =ban;
		this.totalScore =totalScore;
	}

	public String toString() { 
	   return String.format("[%s, %d, %d]", name, ban, totalScore); 
	}

	String getName()     { return name; }
	int getBan()         { return ban; }
	int getTotalScore()  { return totalScore; }

	// 총점 내림차순을 기본 정렬로 한다. 
	public int compareTo(Student s) { 
		return s.totalScore - this.totalScore;
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
[김자바, 1, 200]
[소자바, 1, 200]
[박자바, 2, 150]
[안자바, 2, 100]
[이자바, 3, 300]
[나자바, 3, 290]
[감자바, 3, 180]
```

학생의 성적 정보를 요소로 하는 Stream&#60;Student&#62;을 반별로 정렬한 다음에, 총점별 내림차순으로 정렬한다. 정렬하는 코드를 짧게 하려고, Comparable을 구현해서 총점별 내림차순 정렬이 Student클래스의 기본 정렬이 되도록 했다.