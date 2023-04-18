---
layout: post
title: 호출스택 (call stack)
categories: Java
description: Java의 정석 기초편 ch. 6 객체지향 프로그래밍 1
date: 2023-04-07 17:31:00 +09:00
---
호출스택(call stack)은 메서드의 작업에 필요한 메모리 공간을 제공한다. 메서드가 호출되면, 호출스택에 호출된 메서드를 위한 메모리가 할당되며, 이 메모리는 메서드가 작업을 수행하는 동안 지역변수(매개변수 포함)들과 연산의 중간 결과 등을 저장하는 데 사용된다. 그리고 메서드가 작업을 마치면 할당되었던 메모리 공간은 반환되어 비워진다.

```java
class Ex6_5 {
  public static void main(String[] args) {
    System.out.println("Hello");
  }
}
```

이 예제를 실행시키면 다음 순서로 프로그램이 실행된다.

1. JVM에 의해서 main 메서드가 호출됨으로써 프로그램이 시작된다. 이때, 호출스택에는 main 메서드를 위한 메모리 공간이 할당되고 main 메서드의 코드가 수행되기 시작한다.
2. main 메서드에서 println()을 호출한 상태이다. 아직 main 메서드가 끝난 것은 아니므로 main 메서드는 호출스택에 대기 상태로 남아있고 println()의 수행이 시작된다. println 메서드에 의해 'Hello'가 화면에 출력된다.
3. println 메서드의 수행이 완료되어 호출스택에서 사라지고 자신을 호출한 main 메서드로 되돌아간다. 대기 중이던 main 메서드는 println()을 호출한 이후부터 수행을 재개한다.
4. main 메서드에도 더 이상 수행할 코드가 없으므로 종료되어, 호출스택은 완전히 비워지게 되고 프로그램은 종료된다.


호출스택을 조사해 보면 메서드 간의 호출 관계와 현재 수행 중인 메서드가 어느 것인지 알 수 있다.


### 호출스택의 특징

- 메서드가 호출되면 수행에 필요한 만큼의 메모리를 스택에 할당 받는다.
- 메서드가 수행을 마치고 나면 사용했던 메모리를 반환하고 스택에서 제거된다.
- 호출스택의 제일 위에 있는 메서드가 현재 실행 중인 메서드이다.
- 아래에 있는 메서드가 바로 위에 있는 메서드를 호출한 메서드이다.


반환타입(return type)이 있는 메서드는 종료되면서 결괏값을 자신을 호출한 메서드(caller)에게 반환한다. 대기 상태에 있던 호출한 메서드(caller)는 넘겨 받은 반환값으로 수행을 계속 진행하게 된다.