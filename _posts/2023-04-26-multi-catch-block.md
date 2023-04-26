---
layout: post
title: 멀티 catch블럭
description: Java의 정석 기초편 ch. 8 예외처리
categories: Java
date: 2023-04-26 14:48:00 +09:00
---
여러 catch블럭을 '\|' 기호를 이용해서 하나의 catch블럭으로 합칠 수 있다. 이를 '**멀티 catch블럭**'이라고 한다.

멀티 catch블럭을 이용하면 중복된 코드를 줄일 수 있다. 그리고 '\|'기호로 연결할 수 있는 예외 클래스의 개수에는 제한이 없다.

(이때 '\|'는 논리 연산자가 아니다.)

```java
try {
  // ...
} catch (ExceptionA e) {
  e.printStackTrace();
} catch (ExceptionB e2) {
  e2.printStackTrace();
}
```

위 코드를 아래와 같이 바꿀 수 있다.

```java
try {
  // ...
} catch (ExceptionA | ExceptionB e) {
  e.printStackTrace();
}
```

만일 멀티 catch블럭의 '\|' 기호로 연결된 예외 클래스가 조상과 자손의 관계에 있다면 컴파일 에러가 발생한다.

```java
try {
  // ...
// } catch (ParentException | ChildException e) {  // ERROR! 조상과 자손 관계 
} catch (ParentException e) { // OK. 위의 라인과 의미상 동일
  e.printStackTrace();
}
```

두 예외 클래스가 조상과 자손의 관계에 있다면, 그냥 조상 클래스만 써주는 것과 똑같기 때문이다. 불필요한 코드는 제거하라는 뜻에서 에러가 발생하는 것이다.

그리고 멀티 catch는 하나의 catch블럭으로 여러 예외를 처리하는 것이기 때문에, 발생한 예외를 멀티 catch블럭으로 처리하게 되었을 때, 멀티 catch블럭 내에서는 실제로 어떤 예외가 발생한 것인지 알 수 없다. 그래서 참조변수 e로 멀티 catch블럭에 '\|' 기호로 연결된 예외 클래스들의 공통 분모인 조상 예외 클래스에 선언된 멤버만 사용할 수 있다.

```java
try {
  // ...
} catch (ExceptionA | ExceptionB e) {
  e.methodA();  // ERROR. ExceptionA에 선언된 methodA()는 호출 불가
  
  if (e instanceof ExceptionA) {
    ExceptionA e1 = (ExceptionA)e;
    e1.methodA(); // OK. ExceptionA에 선언된 메서드 호출 가능 
  } else {  // if (e instanceof ExceptionB)
    // ...
  }
}
```
