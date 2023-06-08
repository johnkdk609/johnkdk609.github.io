---
layout: post
title: 어노테이션 타입 정의하기, 어노테이션의 요소
description: Java의 정석 기초편 ch. 12 지네릭스, 열거형, 애너테이션
categories: Java
date: 2023-06-08 11:54:00 +0900
---
이제 직접 어노테이션을 만들어서 사용해볼 차례이다. 새로운 어노테이션을 정의하는 방법은 아래와 같다. '@' 기호를 붙이는 것을 제외하면 인터페이스를 정의하는 것과 동일하다.

```java
@interface 어노테이션이름 {
  타입 요소이름();    // 어노테이션의 요소를 선언한다.
    ...
}
```

엄밀히 말해서 '@Override'는 어노테이션이고 'Override'는 '어노테이션의 타입'이다.


## 어노테이션의 요소

어노테이션 내에 선언된 메서드를 '어노테이션의 요소(element)'라고 하며, 아래에 선언된 TestInfo어노테이션은 다섯 개의 요소를 갖는다.

(어노테이션에도 인터페이스처럼 상수를 정의할 수 있지만, 디폴트 메서드는 정의할 수 없다.)

```java
@interface TestInfo {
  int count();
  String testedBy();
  String[] testTools();
  TestType testType();    // enum TestType { FIRST, FINAL }
  DateTime testDate();    // 자신이 아닌 다른 어노테이션(@DateTime)을 포함할 수 있다.
}

@interface DateTime {
  String yymmdd();
  String hhmmss();
}
```

어노테이션의 요소는 반환값이 있고 매개변수는 없는 추상 메서드의 형태를 가지며, 상속을 통해 구현하지 않아도 된다. 다만, 어노테이션을 적용할 때 이 요소들의 값을 빠짐없이 지정해주어야 한다. 요소의 이름도 같이 적어주므로 순서는 상관없다.

```java
@TestInfo(
  count = 3, testedBy="Kim",
  testTools={"JUnit", "AutoTester"},
  testType=TestType.FIRST,
  testDate=@DateTime(yymmdd="160101", hhmmss="235959")
)
public class NewClass { ... }
```

어노테이션의 각 요소는 기본값을 가질 수 있으며, 기본값이 있는 요소는 어노테이션을 적용할 때 값을 지정하지 않으면 기본값이 사용된다.

(기본값으로 null을 제외한 모든 리터럴이 가능하다.)

```java
@interface TestInfo {
  int count() default 1;    // 기본값을 1로 지정
}

@TestInfo   // @TestInfo(count=1)과 동일
public class NewClass { ... }
```

어노테이션의 요소가 오직 하나뿐이고 이름이 value인 경우, 어노테이션을 적용할 때 요소의 이름을 생략하고 값만 적어도 된다.

```java
@interface TestInfo {
  String value();
}

@TestInfo("passed")   // @TestInfo(value="passed")와 동일
class NewClass { ... }
```

요소의 타입이 배열인 경우, 괄호{}를 사용해서 여러 개의 값을 지정할 수 있다.

```java
@interface TestInfo {
  String[] testTools();
}

@Test(testTools={"JUnit", "AutoTester"})    // 값이 여러 개인 경우
@Test(testTools="JUnit")                    // 값이 하나일 때는 괄호{} 생략 가능
@Test(testTools={})                         // 값이 없을 때는 괄호{}가 반드시 필요
```

기본값을 지정할 때도 마찬가지로 괄호{}를 생략할 수 있다.

```java
@interface TestInfo {
  String[] info() default {"aaa", "bbb"};   // 기본값이 여러 개인 경우. 괄호{} 사용
  String[] info2() default "ccc";           // 기본값이 하나인 경우. 괄호 생략 가능
}

@TestInfo             // @TestInfo(info={"aaa", "bbb"}, info2="ccc")와 동일
@TestInfo(info={})    // @TestInfo(info={"aaa", "bbb"}, info2={})와 동일
class NewClass { ... }
```

요소의 타입이 배열일 때도 요소의 이름이 value이면, 요소의 이름을 생략할 수 있다. 예를 들어, '@SuppressWarnings'의 경우, 요소의 타입이 String배열이고 이름이 value이다.

```java
@interface SuppressWarnings {
  String[] value();
}
```

그래서 어노테이션을 적용할 때 요소의 이름을 생략할 수 있는 것이다.

```java
// @SuppressWarnings(value={"deprecation", "unchecked"})
@SuppressWarnings({"deprecation", "unchecked"})
class NewClass { ... }
```
