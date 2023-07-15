---
layout: post
title: Predicate의 결합
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-15 23:05:00 +0900
---
여러 조건식을 논리 연산자인 &&(and), ||(or), !(not)으로 연결해서 하나의 식을 구성할 수 있는 것처럼, 여러 Predicate를 and(), or(), negate()로 연결해서 하나의 새로운 Predicate로 결합할 수 있다.

```java
Predicate<Integer> p = i -> i < 100;
Predicate<Integer> q = i -> i < 200;
Predicate<Integer> r = i -> i % 2 == 0;
Predicate<Integer> notP = p.negate();       // i >= 100

// 100 <= i && (i < 200 || i % 2 == 0)
Predicate<Integer> all = notP.and(q.or(r));
System.out.println(all.test(150));          // true
```

이처럼 and(), or(), negate()로 여러 조건식을 하나로 합칠 수 있다. 물론 아래와 같이 람다식을 직접 넣어도 된다.

```java
Predicate<integer> all = notP.and(i -> i < 200).or(i -> i % 2 == 0);
```

(Predicate의 끝에 negate()를 붙이면 조건식 전체가 부정이 된다.)

그리고 static메서드인 isEqual()은 두 대상을 비교하는 Predicate를 만들 때 사용한다. 먼저, isEqual()의 매개변수로 비교대상을 하나 지정하고, 또 다른 비교대상은 test()의 매개변수로 지정한다.

```java
Predicate<String> p = Predicate.isEqual(str1);
boolean result = p.test(str2);      // str1과 str2가 같은지 비교하여 결과를 반환
```

위의 두 문장을 합치면 아래와 같다. 오히려 아래의 문장이 이해하기 더 쉬울 것이다.

```java
// str1과 str2가 같은지 비교
boolean result = Predicate.isEqual(str1).test(str2);
```


## Predicate의 결합 예제

```java
import java.util.function.*;

class Ex14_3 {
	public static void main(String[] args) {
		Predicate<Integer> p = i -> i < 100;
		Predicate<Integer> q = i -> i < 200;
		Predicate<Integer> r = i -> i % 2 == 0;
		Predicate<Integer> notP = p.negate();   // i >= 100

		Predicate<Integer> all = notP.and(q.or(r));
		System.out.println(all.test(150));      // true

		String str1 = "abc";
		String str2 = "abc";
		
		// str1과 str2가 같은지 비교한 결과를 반환 
		Predicate<String> p2 = Predicate.isEqual(str1); 
		boolean result = p2.test(str2);   
		System.out.println(result);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
true
true
```