---
layout: post
title: Optional&#60;T&#62;
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-23 09:08:00 +0900
---
Optional&#60;T&#62;은 "T타입의 객체"를 감싸는 래퍼 클래스이다. 그래서 Optional타입의 객체에는 모든 타입의 객체를 담을 수 있다.

(java.util.Optional은 JDK1.8부터 추가되었다.)

```java
public final class Optional<T> {
    private final T value;      // T타입의 참조변수
        ...
}
```

최종 연산의 결과를 그냥 반환하는 게 아니라 Optional객체에 담아서 반환을 하면, 반환된 결과가 null인지 매번 if문으로 체크하는 대신 Optional에 정의된 메서드를 통해서 간단히 처리할 수 있다.

Optional&#60;T&#62;를 이용하면, null 체크를 위한 if문 없이도 NullPointerException이 발생하지 않는 보다 간결하고 안전한 코드를 작성하는 것이 가능하다.


## Optional&#60;T&#62;객체 생성하기

Optional객체를 생성할 때는 of() 또는 ofNullable()을 사용한다.

```java
String str = "abc";
Optional<String> optVal = Optional.of(str);
Optional<String> optVal = Optional.of("abc");
Optional<String> optVal = Optional.of(new String("abc"));
```

만일 참조변수의 값이 null일 가능성이 있으면, of() 대신 ofNullable()을 사용해야 한다. of()는 매개변수의 값이 null이면 NullPointerException이 발생하기 때문이다.

```java
Optional<String> optVal = Optional.of(null);    // NullPointerException발생
Optional<String> optVal = Optional.ofNullable(null);    // OK
```

Optional&#60;T&#62;타입의 참조변수를 기본값으로 초기화할 때는 empty()를 사용한다. null로 초기화하는 것이 가능하지만, empty()로 초기화하는 것이 바람직하다.

(empty()는 제네릭 메서드라서 앞에 &#60;T&#62;를 붙였다. 추정 가능하므로 생략할 수 있다.)

```java
Optional<String> optVal = null;     // null로 초기화. 바람직하지 않음
Optional<String> optVal = Optional.<String>empty();     // 빈 객체로 초기화
```

## Optional&#60;T&#62;객체의 값 가져오기

Optional객체에 저장된 값을 가져올 때는 get()을 사용한다. 값이 null일 때는 NoSuchElementException이 발생하며, 이를 대비해서 orElse()로 대체할 값을 지정할 수 있다.

```java
Optional<String> optVal = Optional.of("abc");
String str1 = optVal.get();         // optVal에 저장된 값을 반환. null이면 예외 발생
String str2 = optVal.orElse("");    // optVal에 저장된 값이 null일 때는 ""를 반환
```

orElse()의 변형으로는 null을 대체할 값을 반환하는 람다식을 지정할 수 있는 orElseGet()과 null일 때 지정된 예외를 발생시키는 orElseThrow()가 있다.

```java
T orElseGet(Supplier<? extends T> other)
T orElseThrow(Supplier<? extends X> exceptionSupplier)
```

사용하는 방법은 아래와 같다.

```java
String str3 = optVal2.orElseGet(String::new);   // () -> new String()와 동일
String str4 = optVal2.orElseThrow(NullPointerException::new);   // null이면 예외 발생
```

isPresent()는 Optional객체의 값이 null이면 false()를, 아니면 true를 반환한다. ifPresent(Consumer&#60;T&#62; block)은 값이 있으면 주어진 람다식을 실행하고, 없으면 아무 일도 하지 않는다.

```java
if (str != null) {
    System.out.println(str);
}
```

만일 위와 같은 조건문이 있다면, isPresent()를 이용해서 다음과 같이 쓸 수 있다.

```java
if (Optional.ofNullable(str).isPresent()) {
    System.out.println(str);
}
```

이 코드를 ifPresent()를 이용해서 바꾸면 더 간단히 할 수 있다. 아래의 문장은 참조변수 str이 null이 아닐 때만 값을 출력하고, null이면 아무 일도 일어나지 않는다.

```java
Optional.ofNullable(str).ifPresent(System.out::println);
```


## OptionalInt, OptionalLong, OptionalDouble

IntStream과 같은 기본형 스트림의 최종 연산의 일부는 Optional 대신 기본형을 값으로 하는 OptionalInt, OptionalLong, OptionalDouble을 반환한다. 아래는 IntStream에 정의된 메서드들이다.

```java
OptionalInt findAny()
OptionalInt findFirst()
OptionalInt reduce(IntBinaryOperator op)
OptionalInt max()
OptionalInt min()
OptionalInt average()
```

반환 타입이 Optional&#60;T&#62;가 아니라는 것을 제외하고는 Stream에 정의된 것과 유사하지만 기본형 Optional에 저장된 값을 꺼낼 때 사용하는 메서드는 이름이 조금씩 다르다는 것에 주의해야 한다.

<table>
    <tr>
        <th>Optional클래스</th>
        <th>값을 반환하는 메서드</th>
    </tr>
    <tr>
        <td>Optional&#60;T&#62;</td>
        <td>T get()</td>
    </tr>
    <tr>
        <td>OptionalInt</td>
        <td>int getAsInt()</td>
    </tr>
    <tr>
        <td>OptionalLong</td>
        <td>long getAsLong()</td>
    </tr>
    <tr>
        <td>OptionalDouble</td>
        <td>double getAsDouble()</td>
    </tr>
</table>

OptionalInt는 다음과 같이 정의되어 있다. 앞서 래퍼 클래스에 대해서 배웠으므로, 이렇게 정의되어 있을 것이라고 짐작하는 것은 그리 어려운 일이 아니었을 것이다.

```java
public final class OptionalInt {
        ...
    private final boolean isPresent;    // 값이 저장되어 있으면 true
    private final int value;            // int타입의 변수
}
```

기본형 int의 기본값은 0이므로 아무런 값도 갖지 않는 OptionalInt에 저장되는 값은 0일 것이다. 그러면, 아래의 두 OptionalInt객체는 같은 것일까?

```java
OptionalInt opt = OptionalInt.of(0);        // OptionalInt에 0을 저장
OptionalInt opt2 = OptionalInt.empty();     // OptionalInt에 0을 저장
```

다행히 저장된 값이 없는 것과 0이 저장된 것은 isPresent라는 인스턴스 변수로 구분이 가능하다. isPresent()는 이 인스턴스변수의 값을 반환한다.

```java
System.out.println(opt.isPresent());    // true
System.out.println(opt2.isPresent());   // false
System.out.println(opt.equals(opt2));   // flase
```


## Optional&#60;T&#62; 예제

```java
import java.util.*;

class Ex14_8 {
	public static void main(String[] args) {
		Optional<String>  optStr = Optional.of("abcde");
		Optional<Integer> optInt = optStr.map(String::length);
		System.out.println("optStr="+optStr.get());
		System.out.println("optInt="+optInt.get());

		int result1 = Optional.of("123")
						    .filter(x->x.length() >0)
						    .map(Integer::parseInt).get();

		int result2 = Optional.of("")
						    .filter(x->x.length() >0)
						    .map(Integer::parseInt).orElse(-1);

		System.out.println("result1=" + result1);
		System.out.println("result2=" + result2);

		Optional.of("456").map(Integer::parseInt)
					      .ifPresent(x->System.out.printf("result3=%d%n",x));

		OptionalInt optInt1  = OptionalInt.of(0);   // 0을 저장 
		OptionalInt optInt2  = OptionalInt.empty(); // 빈 객체를 생성 

		System.out.println(optInt1.isPresent());   // true
		System.out.println(optInt2.isPresent());   // false

		System.out.println(optInt1.getAsInt());  // 0
//		System.out.println(optInt2.getAsInt());  // NoSuchElementException
		System.out.println("optInt1="+optInt1);
		System.out.println("optInt2="+optInt2);
	  	System.out.println("optInt1.equals(optInt2)?"+optInt1.equals(optInt2));
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
optStr=abcde
optInt=5
result1=123
result2=-1
result3=456
true
false
0
optInt1=OptionalInt[0]
optInt2=OptionalInt.empty
optInt1.equals(optInt2)?false
```