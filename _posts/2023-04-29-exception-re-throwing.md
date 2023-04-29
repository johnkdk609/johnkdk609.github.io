---
layout: post
title: 예외 되던지기(exception re-throwing)
description: Java의 정석 기초편 ch. 8 예외처리
categories: Java
date: 2023-04-29 13:59:00 +09:00
---
한 메서드에서 발생할 수 있는 예외가 여럿인 경우, 몇 개는 try-catch문을 통해서 메서드 내에서 자체적으로 처리하고, 그 나머지는 선언부에 지정하여 호출한 메서드에서 처리하도록 함으로써, 양쪽에서 나눠서 처리하도록 할 수 있다. 

그리고 심지어는 단 하나의 예외에 대해서도 예외가 발생한 메서드와 호출한 메서드, 양쪽에서 처리하도록 할 수 있다.

이것은 예외를 처리한 후에 인위적으로 다시 발생시키는 방법을 통해서 가능한데, 이것을 '**예외 되던지기(exception re-throwing)**'라고 한다.

먼저 예외가 발생할 가능성이 있는 메서드에서 try-catch문을 사용해서 예외를 처리해주고 catch문에서 필요한 작업을 행한 후에 throw문을 사용해서 예외를 다시 발생시킨다. 다시 발생한 예외는 이 메서드를 호출한 메서드에게 전달되고 호출한 메서드의 try-catch문에서 예외를 또 다시 처리한다.

이 방법은 하나의 예외에 대해서 예외가 발생한 메서드와 이를 호출한 메서드 양쪽 모두에서 처리해줘야 할 작업이 있을 때 사용된다. 이때 주의할 점은 예외가 발생할 메서드에서는 try-catch문을 사용해서 예외처리를 해줌과 동시에 메서드의 선언부에 발생할 예외를 throws에 지정해줘야 한다는 것이다. 

```java
class Ex8_12 {
	public static void main(String[] args) {
		try {
			method1();		
		} catch (Exception e) {
			System.out.println("main메서드에서 예외가 처리되었습니다.");
		}
	} // main메서드의 끝 

	static void method1() throws Exception {
		try {
			throw new Exception();
		} catch (Exception e) {
			System.out.println("method1메서드에서 예외가 처리되었습니다.");
			throw e;  // 다시 예외를 발생시킨다. 
		}
	} // method1메서드의 끝 
}
```

위 코드의 출력 결과는 다음과 같다.

```
method1메서드에서 예외가 처리되었습니다.
main메서드에서 예외가 처리되었습니다.
```

결과에서 알 수 있듯이 method1()과 main메서드 양쪽의 catch블럭이 모두 수행되었음을 알 수 있다. method1()의 catch블럭에서 예외를 처리하고도 throw문을 통해 다시 예외를 발생시켰다. 그리고 이 예외를 main메서드에서 한 번 더 처리한 것이다.

반환값이 있는 return문의 경우, catch블럭에도 return문이 있어야 한다. 예외가 발생했을 경우에도 값을 반환해야 하기 때문이다.

```java
static int method1() {
  try {
    System.out.println("method1()이 호출되었습니다.");
    return 0; // 현재 실행중인 메서드를 종료한다.
  } catch (Exception e) {
    e.printStackTrace();
    return 1; // catch블럭 내에도 return문이 필요하다.
  } finally {
    System.out.println("method1()의 finally블럭이 실행되었습니다.");
  }
} // method1메서드의 끝
```
