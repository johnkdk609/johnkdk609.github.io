---
layout: post
title: printStackTrace()와 getMessage()
description: Java의 정석 기초편 ch. 8 예외처리
categories: Java
date: 2023-04-26 14:29:00 +09:00
---
예외가 발생했을 때 생성되는 예외 클래스의 인스턴스에는 발생한 예외에 대한 정보가 담겨 있으며, getMessage()와 printStackTrace()를 통해서 이 정보들을 얻을 수 있다.

catch블럭의 괄호()에 선언된 참조변수를 통해 이 인스턴스에 접근할 수 있다. 이 참조변수는 선언된 catch블럭 내에서만 사용 가능하며, 자주 사용되는 메서드는 다음과 같다.

> printStackTrace() - 예외 발생 당시의 호출스택(Call Stack)에 있었던 메서드의 정보와 예외 메시지를 화면에 출력한다.\
> getMessage() - 발생한 예외 클래스의 인스턴스에 저장된 메시지를 얻을 수 있다.

```java
class Ex8_5 {
	public static void main(String args[]) {
		System.out.println(1);			
		System.out.println(2);

		try {
			System.out.println(3);
			System.out.println(0/0); // 예외 발생!! 
			System.out.println(4);   // 실행되지 않는다. 
		} catch (ArithmeticException ae) {
			ae.printStackTrace();   // 참조변수 ae를 통해, 생성된 ArithmeticException 인스턴스에 접근할 수 있다.
			System.out.println("예외메시지 : " + ae.getMessage());
		} // try-catch의 끝 

		System.out.println(6);
	} // main 메서드의 끝 
}
```

위 코드의 출력 결과는 다음과 같다.

```
1
2
3
예외메시지 : / by zero
6
java.lang.ArithmeticException: / by zero
	at Ex8_5.main(Ex8_5.java:8)
```

위 예제의 결과는 예외가 발생해서 비정상적으로 종료 되었을 때의 결과와 비슷하지만 예외는 try-catch문에 의해 처리되었으며 프로그램은 정상적으로 종료되었다.

그 대신 ArithmeticException 인스턴스의 printStackTrace()를 사용해서, 호출스택(call stack)에 대한 정보와 예외 메시지를 출력하였다. 이처럼 try-catch문으로 예외처리를 하여 예외가 발생해도 비정상적으로 종료하지 않도록 해주는 동시에, printStackTrace() 또는 getMessage()와 같은 메서드를 통해서 예외의 발생 원인을 알 수 있다. 
