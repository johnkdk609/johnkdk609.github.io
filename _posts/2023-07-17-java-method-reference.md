---
layout: post
title: 메서드 참조
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-17 11:51:00 +0900
---
람다식이 하나의 메서드만 호출하는 경우에는 '메서드 참조(method reference)'라는 방법으로 람다식을 간략히 할 수 있다. 예를 들어 문자열을 정수로 변환하는 람다식은 아래와 같이 작성할 수 있다.

```java
Function<String, Integer> f = (String s) -> Integer.parseInt(s);
```

보통은 이렇게 람다식을 작성하는데, 이 람다식을 메서드로 표현하면 아래와 같다.

(람다식은 엄밀히 말하자면 익명클래스의 객체지만 간단히 메서드만 적었다.)

```java
Integer wrapper(String s) {     // 이 메서드의 이름은 의미없다.
    return Integer.parseInt(s);
}
```

이 wrapper메서드는 별로 하는 일이 없다. 그저 값을 받아서 Integer.parseInt()에게 넘겨주는 일만 할 뿐이다. 차라리 이 거추장스러운 메서드를 벗겨내고 Integer.parseInt()를 직접 호출하는 것이 낫지 않을까?

```java
Function<String, Integer> f = (String s) -> Integer.parseInt(s);

                                    ⬇︎

Function<String, Integer> f = Integer::parseInt;    // 메서드 참조
```

위 메서드 참조에서 람다식의 일부가 생략되었지만, 컴파일러는 생략된 부분을 우변의 parseInt메서드의 선언부로부터, 또는 좌변의 Function인터페이스에 지정된 제네릭 타입으로부터 쉽게 알아낼 수 있다.

<table>
    <tr>
        <th>종류</th>
        <th>람다</th>
        <th>메서드 참조</th>
    </tr>
    <tr>
        <td>static메서드 참조</td>
        <td>(x) -> ClassName.method(x)</td>
        <td>ClassName::method</td>
    </tr>
    <tr>
        <td>인스턴스메서드 참조</td>
        <td>(obj, x) -> obj.method(x)</td>
        <td>ClassName::method</td>
    </tr>
    <tr>
        <td>특정 객체 인스턴스메서드 참조</td>
        <td>(x) -> obj.method(x)</td>
        <td>obj::method</td>
    </tr>
</table>

> 하나의 메서드만 호출하는 람다식은 '클래스이름::메서드이름' 또는 '참조변수::메서드이름'으로 바꿀 수 있다.


## 생성자의 메서드 참조

생성자롤 호출하는 람다식도 메서드 참조로 변환할 수 있다.

```java
Supplier<MyClass> s = () -> new MyClass();      // 람다식
Supplier<MyClass> s = MyClass::new;             // 메서드 참조
```

매개변수가 있는 생성자라면, 매개변수의 개수에 따라 알맞은 함수형 인터페이스를 사용하면 된다. 필요하다면 함수형 인터페이스를 새로 정의해야 한다.

```java
Function<Integer, MyClass> f = (i) -> new MyClass(i);       // 람다식
Function<Integer, MyClass> f2 = MyClass::new;               // 메서드 참조

BiFunction<Integer, String, MyClass> bf = (i, s) -> new MyClass(i, s);
BiFunction<Integer, String, MyClass> bf2 = MyClass::new;    // 메서드 참조
```

그리고 배열을 생성할 때는 아래와 같이 하면 된다.

```java
Function<Integer, int[]> f = x -> new int[x];       // 람다식
Function<Integer, int[]> f2 = int[]::new;           // 메서드 참조
```

메서드 참조는 람다식을 마치 static변수처럼 다룰 수 있게 해준다. 메서드 참조는 코드를 간략히 하는 데 유용해서 많이 사용된다. 람다식을 메서드 참조로 변환하는 연습을 많이 해서 빨리 익숙해져야 한다.

(메서드 참조를 람다식으로 바꿔보면 이해가 쉬워진다.)