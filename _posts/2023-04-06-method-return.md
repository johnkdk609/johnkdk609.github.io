---
layout: post
title: 메서드의 return문
categories: Java
description: Java의 정석 기초편 ch.6 객체지향 프로그래밍 1
date: 2023-04-06 22:29:00 +09:00
---
return문은 현재 실행중인 메서드를 종료하고 호출한 메서드로 되돌아가게 한다.

원래 반환값의 유무에 관계없이 모든 메서드에는 적어도 하나의 return문이 있어야 한다. 그런데도 반환타입이 void인 경우, return문 없이도 아무런 문제가 없었던 이유는 **컴파일러가 메서드의 마지막에 'return;'을 자동적으로 추가해주었기 때문**이다.

```java
void printGugudan(int dan) {
  for (int i = 1; i <= 9; i++) {
    System.out.printf("%d * %d = %d%n, dan, i, dan * i);
  }
  return; // 반환 타입이 void이므로 생략 가능. 컴파일러가 자동으로 추가
}
```

그러나 반환타입이 void가 아닌 경우, 즉 반환값이 있는 경우, 반드시 return문이 있어야 한다. return문이 없으면 컴파일 에러(error: missing return statement)가 발생한다.

```java
int multiply(int x, int y) {
  int result = x * y;
  
  return result;  // 반환 타입이 void가 아니므로 생략 불가
}
```


아래의 코드는 두 값 중에서 큰 값을 반환하는 메서드이다. 이 메서드의 리턴 타입이 int이고 int 타입의 값을 반환하는 return문이 있지만, return문이 없다는 에러가 발생한다.

왜냐하면 if문 조건식의 결과에 따라 return문이 실행되지 않을 수도 있기 때문이다.

```java
int max(int a, int b) {
  if (a > b) {
    return a; // ERROR: 조건식이 참일 때에만 실행된다.
  }
}
```

그래서 이런 경우 다음과 같이 if문의 else블럭에 return문을 추가해서, 항상 결괏값이 반환되도록 해야 한다.

```java
int max(int a, int b) {
  if (a > b) {
    return a; // 조건식이 참일 때 실행된다.
  } else {
    return b; // 조건식이 거짓일 때 실행된다.
  }
}
```


한편, 메서드의 반환타입이 'void'가 아닌 경우, 구현부{} 안에 'return 반환값;'이 반드시 포함되어 있어야 한다. 이 문장은 작업을 수행한 결과인 반환값을 호출한 메서드로 전달하는데, 이 **값의 타입은 반환 타입과 일치하거나 적어도 자동 형변환이 가능한 것이어야** 한다.

여러 개의 변수를 선언할 수 있는 매개변수와 달리 return문은 단 하나의 값만 반환할 수 있는데, 메서드로의 입력(매개변수)은 여러 개일 수 있어도 출력(반환값)은 최대 하나만 허용하는 것이다.

```java
int add(int x, int y) { // 선언부 int
  int result = x + y;
  return result;        // 반환하는 값 int 일치
}
```


return문의 반환값으로 주로 변수가 오긴 하지만 항상 그런 것은 아니다.

```java
int add(int x, int y) {
  int result = x + y;
  return result;
}
```

위 코드를 이렇게 변경할 수 있다.

```java
int add(int x, int y) {
  return x + y;
}
```

변경된 코드를 보면, return문의 반환값으로 'x+y'라는 수식이 적혀있다. 그렇다고 해서 수식이 그대로 반환되는 것은 아니고, 이 수식을 계산한 결과가 반환된다.


간단한 메서드의 경우 if문 대신 조건 연산자를 사용하기도 한다. 메서드 abs는 입력 받는 정수의 부호를 판단해서 음수일 경우 부호연산자(-)를 사용해서 양수로 반환한다.

```java
//int abs(int x) {
//  if (x >= 0) {
//    return x;
//  } else {
//    return -x;
//  }
//}

int abs(int x) {
  return x >= 0 ? x : -x;
}
```
