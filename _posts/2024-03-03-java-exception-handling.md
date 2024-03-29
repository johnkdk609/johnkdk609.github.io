---
layout: post
title: 예외 처리 (Exception Handling)
categories: Java
date: 2024-03-03 23:19:00 +0900
---
예외 처리(Exception Handling)에 대해 알아보겠다.

프로그램은 항상 완벽하게 동작하지 않는다. 설령 프로그램이 완벽하더라도, 다른 것들과 상호작용하는 과정에서 불확실성이 존재하기 마련이다. 불확실성이 존재하는 것이다.

예외 처리는 이러한 불확실성에 어떻게 대처하는지에 대한 내용이라고 할 수 있다.

<br>

## 프로그램의 오류

프로그램에서 '오류'란 무엇일까? 어떤 원인에 의해 오동작 하거나 비정상적으로 종료되는 경우를 오류라고 할 수 있겠다. 버그라고 부르기도 한다.

오류는 <u>발생시점에 따라 구분이 가능</u>하다.

* <b>컴파일 에러 (compile-time error)</b>
* <b>런타임 에러 (runtime error)</b>
* <b>논리적 에러 (logical error)</b>

먼저 컴파일 에러는 컴파일이 되지 않는 것이다. <u>문법 오류의 이유로 컴파일 조차 진행되지 않는 것</u>이다. 기계어로 번역할 수 없는 경우이다.

런타임 에러는 일단 컴파일은 되었다. 실행도 되었다. 그런데 <u>실행 과정에서 어떤 문제가 발생한 것</u>이다. 예를 들어서, nullPointerError, IndexOutOfBoundsError 등이 있다. 시점을 보면 컴파일이 된 이후이다.

마지막은 논리적 에러이다. 논리적 에러는 실행도 다 되었고, 실행 과정에서 문제가 없었는데, <u>결과가 내가 원하는 결과가 아닌 경우</u>이다. 의도와 다르게 결과가 나온 것이라고 할 수 있다.

<br>

## 에러와 예외

에러와 예외는 자바에서 구별을 해준다. 이 둘은 차이가 있다. 결정적인 차이는 <b>"개발자가 고칠 수 있느냐 없느냐"</b>이다.

예외는 처리를 하는 것이고, 에러는 따로 우리가 처리하지 않는다. 개발자가 고칠 수 있는 것이 예외이고, 에러는 고칠 수 없는 것이다.

* <b>Error : 발생하면 복구할 수 없는 심각한 오류</b>
    * An Error is a subclass of Throwable that indicates serious problems that <u>a reasonable application should not try to catch.</u> ⇒ 자바 공식 문서에서도 합리적인 프로그램이라면 신경 쓸 필요 없다고 언급한다.
    * 메모리 부족, 스택 오버 플로우 등...

가령 자바에서는 스택의 깊이가 보통 8,000 정도로 제한되어 있고, 이는 우리가 어쩔 수 없는 것이다. 우리가 어떻게 할 수 없는 경우인 것이다.

<br>

* <b>Exception : 프로그래머가 적절한 코드를 통해 대비할 수 있는 오류</b>
    * An <i>Exception</i> is an event, which occurs during the execution of a program, that disrupts the normal flow of the program's instructions. ⇒ 예외는 'Exceptional event'의 줄임말이다. 실행 과정이나 컴파일 과정에서 나타날 수 있는 사건이고, 정상적인 흐름을 방해하는 것으로 대처할 수 있다는 것이다.
    * 클래스 형변환 실패, 파일 읽기 실패 등...

<br>

정리해보면, 에러는 우리가 처리하지 않는 것이지만 예외는 우리가 처리하는 것이다.

<br>

## 예외 처리 (Exception Handling)

원래 예외가 발생하면 비정상적으로 종료가 되어 버린다. 그런데 예외 처리를 하면 정상적인 실행 상태를 유지할 수 있다. Plan B 를 할 수 있는 것으로 예외가 발생했을 때 다른 대처 코드를 실행할 수 있다.

예외 처리의 또 다른 중요한 측면은, 예외의 감지 및 예외 발생 시 동작할 코드를 작성함으로써 예방을 할 수 있다는 것이다.

<br>

협업을 하는 과정에서, 전체적으로 커다란 프로그램을 만들어 가는데 A 팀이 A 클래스를 작성했고 B 팀이 B 클래스를 작성했다고 해보자. 

만약에 A 클래스에서 예외가 발생할 가능성이 있으면, 선언부에 throws 키워드를 사용해 어떤 예외가 발생 가능한지 메서드에 정의해놓을 수 있다. 이렇게 해두면 협업을 하는 과정에서, B 클래스에서 A 클래스의 객체와 상호작용할 때 (메서드를 호출한다는 것) 미리 알 수 있다. 

B 클래스 입장에서는 A 클래스를 사용하는 데 있어서 문제점이 있을 수 있다는 것을 사전에 파악할 수 있고, 대처 코드를 작성할 수 있다.

<br>

이런 것들이 협업할 때 매우 중요한 것이다.

<br>

## 예외 클래스의 계층

예외도 자바에서는 객체이다. 다 클래스로 되어있는 것이다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1990b4a9-4e53-474e-8d3d-8ab61a406bf7" width="1200" alt="Java Exception Hierarchy" title="https://thistechnologylife.com/how-to-effectively-handle-exceptions/">

<br>

예외도 클래스이기 때문에, 제일 위에 Throwable은 Object의 자식이다. (Object는 모든 객체의 아버지이다.)

그 밑에 Throwable이 있는데, 'throw 할 수 있는 것'이라는 것이다.

Throwable의 자식들은 크게 세 분류로 나뉜다.

우선 Error는 오류가 발생하지만 우리가 크게 신경 쓸 필요가 없는 것들이다. 우리가 어떻게 할 수 없어서 처리를 할 수 없는 것들이다.

다음으로 Exception은 보통, <b>CheckedExcpetion</b>을 지칭한다. 위 그림에서 초록색 부분들이다. CheckedException은 컴파일 할 때 체크한다.

마지막으로 RuntimeException은 Exception이기는 한데 컴파일할 때 체크하는 것이 아니고, 체크하지 않는 <b>UncheckedException</b>이다.

<br>

Exception은 보통 컴파일 에러인데, 이 중에서 RuntimeException을 상속받은 것들은 런타임 에러이다. 다시 말하면, Exception의 자손들 중에서 RuntimeException이 아닌 것들은 컴파일 에러로 볼 수 있다.

<br>

정리하자면 다음과 같다.

* 예외의 계열
    * <b>RuntimeException 클래스들</b>
        * 프로그래머의 실수로 발생하는 계열
        * <i>unchecked exception</i>
        * 대처 코드가 없어도 컴파일 문제 X ⇒ 컴파일은 문제 없이 진행된다. 실행까지 가므로, 실행 과정에서 발생할 수 있다.
    * <b>(RuntimeException이 아닌) Exception 클래스들</b>
        * 사용자의 실수 등 외적인 요인에 의해 발생하는 계열
        * <i>checked exception</i>
        * 대처 코드가 없으면 컴파일 진행 X ⇒ 실행 자체가 안 된다는 것.

<br>

예시 코드를 통해 예외를 보겠다.

```java
package test01_exception;

public class ExceptionTest {
	public static void main(String[] args) {
		
		int[] nums = {10};
		
		// UncheckedException
		// - 컴파일 시간에 확인 x -> 빨간 줄 x
		// - 실행 중에 발생
		// - 예외 처리를 강제하지 않음
		// - 예외 처리: try ~ catch를 이용해서 처리할 수도있다.
		System.out.println(nums[2]);
		
		// CheckedException
		// - 컴파일 시간에 확인 -> 빨간 줄
		// - 예외 처리를 강제
		// - 예외처리 방법: try ~ catch를 사용해서 직접처리, throws 선언해서 예외처리 위임(간접처리)
		// Thread.sleep(1000); // 빨간 줄 checked => 컴파일 안됨.
		
		System.out.println("프로그램 종료.");
		
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 2 out of bounds for length 1
	at test01_exception.ExceptionTest.main(ExceptionTest.java:13)
```

위와 같이 출력되었다는 것은 일단 실행은 되었다는 뜻이다. main 함수가 일단 실행이 되었다는 것은, 컴파일이 이미 완료 되었다는 것이다. 결국 위 경우는 RuntimeException으로 UncheckedException이라고 할 수 있겠다.

위 코드에서 만약 ```Thread.sleep(1000);```을 입력하면 코드에 빨간 밑줄이 생긴다. 자바 코드를 저장할 때마다 컴파일이 내부적으로 일어나고 있는데, 컴파일 시간에 확인해서 빨간 밑줄이 켜지고, 이는 예외 처리를 강제하는 기능을 가진다. 예외 처리는 try ~ catch를 사용해서 직접 처리하거나, throws를 선언해서 예외처리를 위임할 수 있다.

CheckedException은 위와 같이 예외처리가 강제가 된다.

<br>

UncheckedException은 예외 처리가 강제되지 않지만, 만약 하고 싶다면 try ~ catch를 이용하면 된다.

<br>

## 예외 처리 키워드

예외를 처리하는 데에는 두 가지 방법이 있다.

* 직접 처리
    * try
    * catch
    * finally
* 간접 처리
    * throws
* 사용자 정의 예외 발생시킬 때
    * throw

<br>

우선 try ~ catch 구문에 대해 알아보겠다.

try ~ catch 구문으로 프로그램 실행 시 발생할 수 있는 예외에 대한 대비코드를 작성할 수 있다. 이는 <u>비정상적인 종료를 막고, 정상적인 상태를 유지할 수 있게 한다.</u>

예외가 발생하면 JVM의 예외처리기가 받아서 처리한다.

```java
try {
    // 예외가 발생할 수 있는 코드
} catch (Exception e) {
    // 예외가 발생했을 때 처리할 코드
}
```

위 코드에서 중괄호(블록)는 생략 불가능하다. 그리고 블럭 안에 또 다른 try ~ catch 구문이 올 수 있다.

<br>

try ~ catch 문을 사용한 예외처리 예시 코드를 보겠다.

```java
package test02_try_catch;

public class ExceptionTest1 {
	public static void main(String[] args) {
		// try ... catch 문을 사용한 예외처리
		
		int[] nums = {10};
		
		// 1. 예외가 발생했고 제대로 처리한 경우 : 1 4 5
		// 2. 예외가 발생했는데 처리하지 못했을 때: 1 (2<-에러) 비정상적 종료
		// 3. 예외가 발생하지 않았을 때? : 1 2 3 5
		
		try {
			// 예외가 발생할 수 있는 코드는 try문 안에 넣는다.
			System.out.println("정상 코드 1"); // 1
			System.out.println(nums[3]);	// 2
			System.out.println("정상 코드 2");	 //3
			
		} catch (ArrayIndexOutOfBoundsException e) { // catch() 예외를 파라미터로 받는다.
			// 예외 대처 코드 작성
			System.out.println("배열의 인덱스 범위를 벗어났습니다."); // 4
		}
		
		System.out.println("프로그램 종료."); //5
	}
}
```

위 코드의 실행 결과는 다음과 같다.

```
정상 코드 1
배열의 인덱스 범위를 벗어났습니다.
프로그램 종료.
```

<br>

위 코드에서 예외가 발생했고 제대로 처리되어, 1, 4, 5가 출력되었다.

만약에 ```catch (ArithmeticException e)```로 잘못 처리했을 경우, 예외가 발생했는데 처리하지 못하게 되므로 1만 작동하고 비정상적으로 종료될 것이다.

만약 예외가 발생하지 않았다면 1, 2, 3, 5가 출력되었을 것이다.

<br>

정리하면 다음과 같다.

```java
try {
    // (1) 예외가 발생할 수 있는 코드
    // (2) 정상코드
} catch (Exception e) {
    // (3) 예외가 발생했을 때 처리할 코드
}
// (4) 일반 코드
```

<b>예외가 발생할 때</b> : 1 3 4

<b>예외가 발생하지 않았을 때</b> : 1 2 4

<br>

## 다중 예외처리

하나의 try에 catch문을 여러 개 쓸 수 있다. 왜냐하면 하나의 try 안에 여러 개의 예외 사항이 올 수 있기 때문이다.

처리하는 방법을 간략하게 보자면 다음과 같다.

```java
try {
    // exception이 발생할 만한 코드
} catch (XXException e) {
    // XXException 발생 시 처리 코드
} catch (YYException e) {
    // YYException 발생 시 처리 코드
}
```

```java
try {
    // exception이 발생할 만한 코드
} catch (XXException | YYException e) {

    // 필요 시 instanceof를 통해 나누어 작성

    // XXException 발생 시 처리 코드
    // YYException 발생 시 처리 코드
}
```

<br>

다중 예외처리를 코드를 통해 알아보겠다.

직전에 했던 ExceptionTest를 다시 한 번 다뤄보겠다.

```java
package test02_try_catch;

public class ExceptionTest2 {
	public static void main(String[] args) {
		// try ... catch 문을 사용한 예외처리
		
		int[] nums = {10};
		
		// 1. 예외가 발생했고 제대로 처리한 경우 : 1 4 5
		// 2. 예외가 발생했는데 처리하지 못했을 때: 1 (2<-에러) 비정상적 종료
		// 3. 예외가 발생하지 않았을 때? : 1 2 3 5
		
		
		try {
			// 예외가 발생할 수 있는 코드는 try문 안에 넣는다.
			System.out.println("정상 코드 1"); // 1
			System.out.println(nums[0]);	// 2
			int num = 4 / 0;
			System.out.println("정상 코드 2");	 //3
			
		} catch(ArrayIndexOutOfBoundsException e) { // catch() 예외를 파라미터로 받는다.
			// 예외 대처 코드 작성
			System.out.println("배열의 인덱스 범위를 벗어났습니다."); // 4
		} catch(ArithmeticException e) {
			System.out.println("계산 오류가 발생했습니다.");
		}
		
		System.out.println("프로그램 종료."); //5
	}
}
```

위 코드의 실행 결과는 다음과 같다.

```
정상 코드 1
10
계산 오류가 발생했습니다.
프로그램 종료.
```

<br>

만약 ```catch (Exception e)```로 예외를 처리하면, 이는 모든 예외를 한꺼번에 처리하는 것이다. 예를 들어 모든 객체를 Object로 처리하는 것이다. Exception을 받는 입장에서는, 이 ```e```가 실제로 배열의 인덱스 범위를 넘어간 것인지, 0으로 나눠서 발생한 예외인지 알 수 없다.

구체적으로 써 줘야 구체적인 Exception에 대한 처리가 가능해진다.

위 코드와 같이 ArrayIndexOutOfBoundsException, ArithmeticException 등으로 구체적으로 처리해줄 수 있다. 4를 0으로 나눴으니, ```catch (ArithmeticException e)```에 걸렸고, 출력 결과를 보면 "계산 오류가 발생했습니다."가 정상적으로 출력되었다.

만약 ```catch (ArrayIndexOutOfBoundsException | ArithmeticException e)```의 방식으로 처리하면, 한꺼번에 처리할 수 있다는 장점은 있지만 실제로 어떤 예외에 걸린 것인지 알기 어렵다.

이렇게 한꺼번에 처리했을 경우, ```if (e instanceof ArithmeticException)```과 같이 instanceof로 확인을 해줘야 한다.

```java
package test02_try_catch;

public class ExceptionTest3 {
	public static void main(String[] args) {
		// try ... catch 문을 사용한 예외처리
		
		int[] nums = {10};
		
		try {
			// 예외가 발생할 수 있는 코드는 try문 안에 넣는다.
			System.out.println("정상 코드 1"); // 1
			System.out.println(nums[3]);	// 2
			int num = 4 / 0;
			System.out.println("정상 코드 2");	 //3
			
		} catch(ArrayIndexOutOfBoundsException | ArithmeticException  e) { // catch() 예외를 파라미터로 받는다.
			// 예외 대처 코드 작성
			System.out.println("배열의 인덱스 범위를 벗어났거나, 계산 오류가 발생했습니다.");
			if(e instanceof ArithmeticException) {
				System.out.println("계산 오류입니다.");
			}
		}
		
		System.out.println("프로그램 종료."); //5
	}
}
```

이렇게 코드를 짜야 하는 것이다. 위 코드의 출력 결과는 다음과 같다.

```
정상 코드 1
배열의 인덱스 범위를 벗어났거나, 계산 오류가 발생했습니다.
프로그램 종료.
```

<br>

초반에 예외 클래스 계층도를 봤었다. 거기서 Exception이 가장 위에 있었고, 그것을 상속 받는 RuntimeException이 있었다. 그리고 그 밑에 다양한 Exception 들이 있었다. Exception이 상속이 이뤄지는 것, 즉 계층이 있는 것이다. 부모와 자식이 있기 때문에, <b>다형성이 여기서도 적용된다. 부모 타입의 클래스로 자식 객체를 참조할 수 있는 것이다.</b>

예외 자체도 클래스이다. 상속 관계가 있을 수도 있고, 형제 관계일 수도 있다. 다형성은 부모 - 자식 인 경우에만 의미가 있다.

* <b>다중 예외처리 유의 사항</b>
	* JVM이 던진 예외는 catch 문장을 찾을 때는 <b>다형성</b>이 적용됨
	* 상위 타입의 예외가 먼저 선언되는 경우 뒤에 등장하는 catch 블럭은 동작할 기회가 없음
	* 상속 관계가 없는 경우는 무관
	* 상속 관계에서는 작은 범위(자식)에서 큰 범위(조상) 순으로 정의

```java
try {
	// exception이 발생할 만한 코드
} catch (XXException e) {
	// XXException 발생 시 처리 코드
} catch (YYException e) {
	// YYException 발생 시 처리 코드
} catch (Exception e) {
	// Exception 발생 시 처리 코드
}
```

try ~ catch 문이 순서대로 작동하기 때문에, 예외 클래스가 자식 ~> 조상 순으로 와야 한다.

다음과 같은 예시 코드를 보자.

```java
package test02_try_catch;

public class ExceptionTest4 {
	public static void main(String[] args) {
		// try ... catch 문을 사용한 예외처리
		
		int[] nums = {10};
		
		// 1. 예외가 발생했고 제대로 처리한 경우 : 1 4 5
		// 2. 예외가 발생했는데 처리하지 못했을 때: 1 (2<-에러) 비정상적 종료
		// 3. 예외가 발생하지 않았을 때? : 1 2 3 5
		
		try {
			// 예외가 발생할 수 있는 코드는 try문 안에 넣는다.
			System.out.println("정상 코드 1"); // 1
			System.out.println(nums[0]);	// 2
			int num = 4 / 1;
			String str = null;
			str.length();
			System.out.println("정상 코드 2");	 //3
			
		} catch(ArrayIndexOutOfBoundsException e) { // catch() 예외를 파라미터로 받는다.
			// 예외 대처 코드 작성
			System.out.println("배열의 인덱스 범위를 벗어났습니다."); // 4
		} catch(ArithmeticException e) {
			System.out.println("계산 오류가 발생했습니다.");
		} catch(Exception e) {
			System.out.println("모든 예외 처리 가능..");
		}
		
		System.out.println("프로그램 종료."); //5
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
정상 코드 1
10
모든 예외 처리 가능..
프로그램 종료.
```

위 코드를 보면, 문자열 str이 null인데, ```str.length()```로 그 문자열의 길이를 알려고 한다. 그런데 문자열이 null이면 객체가 없는 것이므로, 이렇게 부를 수 없다. 그러면 예외가 발생하는 것이다.

이 예외의 경우, ArrayIndexOutOfBoundsException, ArithmeticException 둘 다 처리하지 못했고, 결국 Exception 클래스가 받았다.

만약 위 코드에서 ```catch (Exception e) {```를 제일 위로 올리면, 'Unreachable catch block' 오류가 발생한다. 위에서 모든 것을 처리하기 때문에, 밑에는 기회가 없다는 것이다. 그래서 이렇게 하지 않는다.

Exception 간에도 다형성이 적용되기 때문에, 순서에 유의해야 하는 것이다.

<br>

## Exception 인스턴스의 주요 메서드

Exception 인스턴스에는 메서드가 있다. 그 중에서 크게 이 두 가지를 주로 사용한다.

1. <b>getMessage()</b> : 발생된 예외에 대한 구체적인 메세지를 반환
2. <b>printStackTrace()</b> : 예외 발생 당시의 호출 스택(Call Stack)을 출력한다.

<br>

Exception은 항상 메세지를 가지고 있다. 이것을 ```e.getMessage()```의 방식으로, getter을 이용해서 가져올 수 있다.

또, ```e.printStackTrace()```를 해서 실행 과정이 순차적으로 스택에 쌓인 것을 추적해서 볼 수 있다. 

앞서 본 ExceptionTest1 에 코드를 추가해서 보면 다음과 같다.

```java
package test02_try_catch;

public class ExceptionTest1 {
	public static void main(String[] args) {
		// try ... catch 문을 사용한 예외처리
		
		int[] nums = {10};
		
		// 1. 예외가 발생했고 제대로 처리한 경우 : 1 4 5
		// 2. 예외가 발생했는데 처리하지 못했을 때: 1 (2<-에러) 비정상적 종료
		// 3. 예외가 발생하지 않았을 때? : 1 2 3 5
		
		try {
			// 예외가 발생할 수 있는 코드는 try문 안에 넣는다.
			System.out.println("정상 코드 1"); // 1
			System.out.println(nums[3]);	// 2
			System.out.println("정상 코드 2");	 //3
			
		} catch (ArrayIndexOutOfBoundsException e) { // catch() 예외를 파라미터로 받는다.
			// 예외 대처 코드 작성
			System.out.println("배열의 인덱스 범위를 벗어났습니다."); // 4
			System.out.println(e.getMessage());
			e.printStackTrace();
		}
		
		System.out.println("프로그램 종료."); //5
	}
}
```

위 코드의 실행 결과는 다음과 같다.

<img width="807" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/46fa93f2-825e-4007-93ee-13c26f4ff035">

<br>

## 예외 던지기 throws

이전까지 알아본 try ~ catch 는 예외를 직접 처리하는 것이었다. 반면 던지기(throws)는 간접 처리이다. 내가 처리하지 않고, 남에게 던지는 것이다. throws라는 키워드로 예외를 던질 수 있다. 

그리고 예외가 여러 개 발생할 수 있으면 ,(컴마)를 이용해서 발생할 가능성이 있는 예외들을 나열할 수 있다.

메서드가 나 자신을 호출한 곳으로 전달을 하는 것이며, 조상 타입의 예외로 처리 가능하다.

<br>

예시 코드를 통해 알아보겠다.

```java
package test03_throws;

// CheckedException
// - 컴파일시 예외 처리를 강제
// - 둘중 하나는 반드시 해야된다.
// - 빨간 줄 => throws해서 위임할 수 있다.
//         => 직접처리(try catch)를 하지않는다면 throws를 써야한다.
public class ThrowTest1 {
	
	public static void main(String[] args) throws ClassNotFoundException {
		method1(); // 결국 예외 처리 하지 않는다.
	}
	
	public static void method1() throws ClassNotFoundException {
		// 호출한 곳에서 다시 처리하거나, 위임
		method2();
	}
	
	public static void method2() throws ClassNotFoundException { // 예외 처리 위임
		Class.forName("SSAFY"); // "SSAFY"라는 이름의 클래스를 로드!
		//1. 직접처리
		//2. 간접처리
		
	}	
}
```

위 코드를 보면, CheckedException을 계속 throws 해서 main에서 처리하는 것을 강제하는 모습을 보인다.

스택을 보면, 제일 처음에 main 함수가 올라간다. main 함수가 실행 되다가 method1()을 만나면 method1()이 스택에 올라간다. 그리고 method1()을 실행하는 시점에서 method2()를 만날 때, method2()가 스택에 올라간다.

이 경우 method2()는 스택의 Top에 있는 것이고, 이게 실행되고 있다. method2()가 실행이 종료되면 스택에서 빠져나온다. (pop) 그러면 이제 method1()가 스택의 top에 있게 되고, 또 method1()이 return 되면 제어권은 main으로 넘어간다. 이제 main도 끝나면 main이 pop 되고 프로그램이 종료된다.

<br>

위 코드에서 method2()는 ```Class.forName("SSAFY")```의 방식으로, "SSAFY"라는 이름의 클래스를 메모리에 로드하게 하였다. (JVM 메모리가 있을 때, 클래스 로더로 "SSAFY"라는 이름의 클래스가 있으면 찾아서 로드하라는 static 메서드)

그런데, 메서드 옆에 throws 키워드를 붙이기 전에는 ```Class.forName("SSAFY")```에 빨간 줄이 그어진다. CheckedException이기 때문이다. 

CheckedException은 두 가지 옵션을 준다.

1. throws를 선언하거나 (간접 처리)
2. 여기서 처리 하거나 (직접 처리)

강제로 처리를 해야 하니까, 위 코드의 경우 throws 키워드를 사용해 간접 처리를 하였다. throws를 하여 method2()에서 처리하지 않고, 넘겨버렸다. 그러면 method2()를 호출한 method1()에게 넘어간다.

method1()의 ```method2()```가 빨간 줄이 그어진다. 예외 처리가 위임된 것이다. 그러면 이 method2()를 호출한 곳에서 처리하거나 또 위임해야 한다. 그래서 method1()에서도 throws 를 사용하여 예외를 위임했다. main에서 처리를 할 수밖에 없는 것이다.

만약에 method2()에서 처리했다면, main 함수 안에 ```method1();```에는 빨간 줄이 생기지 않는다.

<br>

main 메서드에서도 예외 처리를 하지 않으면, 결국 예외가 처리되지 않은 상태로 남는다. (논리적으로 볼 때에 어느 순간에는 예외를 처리해주는 것이 좋다.)

main 메서드에서도 예외를 처리하지 않은 채 실행시키면, 콘솔에 에러 메세지가 나타난다.

결론적으로, <b>CheckedException은 컴파일 시 예외 처리를 강제</b>한다. 빨간 줄이 켜지면, <b>throws를 해서 위임할 수 있다.</b> 그런데 <b>위임했을 때 그 책임은 나를 호출한 곳으로 떠넘겨지고, 어딘가에서는 처리를 해주어야 하는 것이다.</b>

<br>

다른 예시 코드를 보겠다.

```java
package test03_throws;

// UncheckedException
// 빨간 줄 없음
// => 예외 처리 강제하지 않는다.
// => throws도 강제하지 않는다.
// => 하고싶으면 try catch로 해라.

// 내부적으로는 예외를 넘기고 있다.
// 결국에 처리가 되지 않았을 때 비정상적 종료.
public class ThrowTest2 {
	public static void main(String[] args) {
		method1();
	}
	
	public static void method1() {
		try {
			method2();
		} catch(ArithmeticException e) {
			System.out.println("method1에서 처리가 될까요?");
		}
	}
	
	public static void method2() {
		int i = 1/0;
	}

}
```

위 코드의 출력 결과는 다음과 같다.

```
method1에서 처리가 될까요?
```

위 코드의 경우, 빨간 줄이 켜지지 않는다. UncheckedException인 것이다. 아까 같은 경우 CheckedException이어서 예외 처리를 강제했다. 그러나 이 경우에는 예외 처리를 강제하지 않는다.

<b>UncheckedException의 경우 throws도 강제하지 않는다.</b>

다른 말로 하면, 하고 싶다면 <b>try ~ catch 문</b>으로 하라는 것이다. CheckedException만 빨간 줄이 켜지는 이유는, 문법적으로 컴파일 때 체크를 하겠다는 것이다. 반면 UncheckedException은 그렇지 않다. 위 코드의 경우에 throws를 하지 않아도 문제 없이 실행된다. (물론 오류가 뜨기는 한다.)

위 코드의 경우, ```method1()``` 안에서 try ~ catch 문으로 처리를 하였다. 그래서 출력 결과가 catch 문 안에 있는 문장으로 잘 나왔다. 이것은 내부적으로 Exception이 넘겨지고 있다는 것이다.

이는 <b>UncheckedException이 CheckedException과 똑같은 점이다. 내부적으로는 예외를 넘기고 있는 것</b>이다. 결국에 처리되지 않았을 때 비정상적인 종료가 된 것이다.

<br>

또 다른 예시 코드를 보자.

```java
package test03_throws;

// CheckedException
// - 컴파일시 예외 처리를 강제
// - 둘중 하나는 반드시 해야된다.
// - 빨간 줄 => throws해서 위임할 수 있다.
//         => 직접처리(try catch)를 하지않는다면 throws를 써야한다.
public class ThrowTest3 {
	
	public static void main(String[] args){
		method1(); // 결국 예외 처리 하지 않는다.
	}
	
	public static void method1()  {
		// 호출한 곳에서 다시 처리하거나, 위임
		try {
			method2();
		}catch(ClassNotFoundException e) {
			// 중간에 try catch로 직접처리했다면 굳이 throws 하지 않아도 된다.
			System.out.println("잘 처리되었음.");
		}
	}
	
	public static void method2() throws ClassNotFoundException { // 예외 처리 위임
		Class.forName("SSAFY"); // "SSAFY"라는 이름의 클래스를 로드!
		//1. 직접처리
		//2. 간접처리
		
	}	
}
```

위 코드의 출력 결과는 다음과 같다.

```
잘 처리되었음.
```

왜냐하면 ClassNotFoundException으로 처리가 되었고, 중간에 try catch 문으로 직접처리를 했기 때문이다.

<br>

checked exception은 throws를 둘 중에 하나는 반드시 해야 하기 때문에 써줬었다. 만약 unchecked exception (runtime exception)이면 throws를 하지 않지만, 내부적으로는 throw 가 되고 있다. 만약 하고 싶으면, 그 시점에 try ~ catch 문을 써야 한다.

<b>전달은 되지만, 내가 처리하고 싶은 시점에 try ~ catch를 써야 한다. 안 그러면 비정상적으로 종료된다.</b>

<br>

### 메서드 재정의와 throws

메서드를 재정의할 때, 조상클래스 메서드가 던지는 예외보다 부모 예외를 던질 수 없다.

다음 예시 코드를 보자.

```java
package test03_throws;

import java.io.FileNotFoundException;
import java.io.IOException;

class Parent {
	public void method1() throws IOException {}
	
	public void method2() throws ClassNotFoundException {}
	
	public void method3() {}
}


// ThrowTest4는 Parent클래스의 자식.
public class ThrowTest4 extends Parent {
	@Override
	public void method1() throws FileNotFoundException {
		
	}
	// 자식은 부모가 던지는 예외보다 조상 예외를 던질 수 없다.
	
	@Override
	public void method2() throws Exception {	// 이 부분이 잘못됨
		
	}
	
	// 하위 클래스의 접근제어자 범위가 상위 클래스보다 크거나 같아야 한다.
	@Override
	public void method3() {}
}
```

위 코드에서 잘못된 부분은 어디에 있을까?

ThrowTest4 클래스의 method2()가 잘못되었다. 부모 클래스의 method2()에서는 ClassNotFoundException를 던지고 있는데, 자식 클래스의 오버라이드 된 method2() 에서 ClassNotFoundException보다 큰 Exception을 던지고 있기 때문이다.

부모가 작은 실수를 하고 있는데, 자식이 더 큰 실수를 하면 안 된다는 것이다.

객체지향에서 부모가 들어갈 수 있는 자리에는 부모 객체가 들어갈 수도 있고, 자식 객체가 들어갈 수도 있다. 부모가 들어가야 할 자리에는 부모의 부품을 넣을 수도 있고, 자식의 부품을 넣을 수도 있다. 그런데 부품을 갈아 끼웠는데 더 큰 실수를 하면 시스템이 제대로 돌아가지 않을 것이다. 그래서 자식은 항상 같거나 작은 예외를 던져야 하는 것이다. 실수를 개선하는 것은 괜찮다. 반면 더 큰 문제가 발생하는 것은 막아야 한다.

<br>

## finally 구문

finally 구문은 예외 발생 여부와 상관 없이 언제나 (always) 실행된다.

```java
try {
	// 예외가 발생할 가능성이 있는 코드
} catch (Exception e) {
	// 예외 처리 코드
} finally {
	// 예외 상관없이 항상 수행해야 하는 코드
}
```

예외가 발생했을 때, try → catch → finally 순으로 코드가 실행된다. 예외가 발생하지 않는다면 try → finally 순으로 코드가 실행된다.

중간에 return이 있어도 finally 블록을 수행한 다음 반환된다.

<br>

예시 코드를 보자.

```java
package test04_finally;

public class FinallyTest1 {
    public static void main(String[] args) {
    	
    	// 정상 실행 시 : 1 2 4 5
    	// 예외 발생 시 : 1 3 4 5
    	// 예외 미처리 시: 1 4 (중단)
    	// catch문에 return이 들어가면(예외발생시): 1 3 4
    	// try문에 return이 들어갔다면(정상실행시): 1 2 4
    	
//    	int[] nums = {10};
        int num = 1;
        
        try {
            System.out.println("1");
            int i = 1 / num;
//            nums[3] = 1;
            System.out.println("2");
            return;
        } catch (ArithmeticException e) {
            System.out.println("3");
         
        } finally {
            System.out.println("4");
            
        }
        System.out.println("5");
    }
}
```

위 코드의 실행 결과는 다음과 같다.

```
1
2
4
```

위 코드를 보면, finally 구문까지만 실행되고 아래에 5는 출력되지 않았다. try 문 안에 return 이 있기 때문이다. return이 있을 경우 finally까지는 무조건 실행되고, 그 이후에 종료된다. 맨 아래에 5는 언제나 실행이 되지 않게 되고, 'unreachable code' 즉 죽은 코드가 된다.

<br>

## 자동 자원 반납 구문 (try with resources)

시스템 자원(resources)을 사용할 때 사용하는 구문이다. 대표적으로 파일 입출력을 할 때 사용한다.

입출력을 하기 위해서는 메모리도 확보해야 하고, 여러 가지 시스템 자원을 소모하는 일이 많다. 그럴 때 적절하게 관리를 해줘야 한다. 만약 작업이 끝났다면 메모리 자원을 반납해줘야 한다.

finally 구문은 무조건 실행되기 때문에 <b>finally에서 close()를 통해 입출력에 대한 자원 반납을 주로 한다.</b>

```java
FileInputStream fis = null;
try {
	fis = new FileInputStream("test.txt");
} catch (IOException e) {
	e.printStackTrace();
} finally {
	try {
		if (fis != null)
			fis.close();
	} catch (IOException e) {
		e.printStackTrace();
	}
}
```

위 코드를 보면, Stream을 열고 "test.txt"라는 파일에 접근해서 데이터를 가져오겠다는 것이다. 이 과정에서 IOException이 발생할 수 있다. 그래서 ```fis = new FileInputStream("test.txt");```는 사실 에러가 발생할 수 있는 코드이다.

예외가 발생 가능하고, CheckedException으로 예외 처리가 강제되기 때문에 try ~ catch 문을 사용해야 한다.

그리고 입력에 대한 자원을 반납할 때에는 finally에서 반납을 해줘야 한다. 그런데 finally 문 안에서도 또 예외가 발생할 수 있기 때문에 위와 같이 또 try ~ catch문을 써야 하는 문제가 발생한다.

그래서 이러한 문제를 try with resources 구문으로 해결하는 것이다.

<br>

try with resources 구문은 다음과 같이 작동한다.

```try ()``` → 괄호 안에 객체를 생성하는 코드를 작성하면, 해당 객체는 close()를 호출하지 않아도 블록을 벗어나는 순간 close()가 호출된다.

해당 객체의 클래스가 AutoCloseable이라는 인터페이스를 구현한 클래스이어야만 한다.

```java
try (FileInputStream fis = new FileInputStream("test.txt")) {
	// 코드 생략
} catch (IOException e) {
	e.printStackTrace();
}
```

<br>

예시 코드를 통해 보겠다.

```java
package test04_finally;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;

public class FinallyTest2 {
    public static void main(String[] args) {
        // try ~ catch ~ finally => try with resources 
        FileInputStream fis = null;
        
        try {	// 이렇게 하면 너무 복잡함...
            fis = new FileInputStream("test.txt");
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } finally {
            try {
                if(fis != null)
                    fis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        
        
        try (FileInputStream fis2 = new FileInputStream("test.txt")){	// try with resources 구문 활용하여 간단하게 표현.
        	
        } catch (IOException e ) {
        	
        }

    }
}
```

위 코드를 보면, try catch finally 문을 쓸 경우 코드가 너무 많고 복잡해진다. 그런데 아랫 부분에 try with resources 구문을 쓰니 코드가 매우 간결해졌다. finally 문을 작성할 필요가 없는 것이다.

자원을 반납하지 않으면 메모리가 계속 자원을 잡아먹고 있는 것이고, 비효율적인 상태가 된다. 시스템 자원을 사용했으면 반납을 해주는 것이다. 안 그러면 프로그램 때문에 시스템에 부하가 점점 커진다.

<br>

## 사용자 정의 예외

이번에는 사용자 정의 예외에 대해 알아보겠다. 기존에 정의된 예외 이외에 사용자가 직접 정의 예외를 작성할 수 있다.

만드는 법은 매우 간단하다. <u>Exception 또는 RuntimeException을 상속 받으면 그게 Exception이 된다.</u> 

* <b>checked exception 활용</b> : 명시적 예외 처리 또는 throws 필요 (코드는 복잡해지지만 처리, 누락 등 오류 발생 가능성은 줄어듦)
* <b>runtime exception 활용</b> : 묵시적 예외 처리 가능 (코드가 간결해지지만 예외 처리, 누락 가능성 발생)

<br>

예시 코드를 보겠다.

```java
package test05_user_exception;

public class UserExceptionTest {

	public static String[] fruits;

	public static void main(String[] args) {

		fruits = new String[] { "사과", "사과", "오렌지", "토마토" };

		boolean result = eatFruit("사과");
		System.out.println(result);
		
		result = eatFruit("사과");
		System.out.println(result);
		
		result = eatFruit("사과");
		System.out.println(result);
		if(result == false) {
			System.out.println("대처코드..");
		}
		
		// 항상 실행결과를 true / false 확인해야 한다.
		// 어떤 이유인지 알 수 없다.
		// 변수가 따로 있어야 되고, 매번 체크.
		// try ~ catch문 쓸수 없고. if문을 복잡하게 써야한다.

	}

	// 예외처리를 이용하지 않는다면...
	// 성공했다면 true를 반환하고 실패했다면 false반환한다.
	public static boolean eatFruit(String name) {
		for (int i = 0; i < fruits.length; i++) {
			if (name.equals(fruits[i])) {
				fruits[i] = null;
				System.out.println(name + "(을)를 먹었습니다.");
				return true;
			}
		}
		return false;
	}

}
```

과일들이 있는데, 과일을 먹는 상황이다. 하나씩 먹는데, 과일이 더 이상 먹을 수 없는 상황이 올 것이다. 만약 기존에 예외 처리를 하지 않았다면 위 코드의 아랫 부분처럼 eatFruit() 메서드의 방식으로 처리해야 할 것이다.

그런데 이렇게 했을 때 문제점은, 구체적으로 에러 메세지를 전달할 수 없다는 것과, 항상 실행 결과를 true / false로 받아서 확인해야 한다는 것이다. 어떤 이유인지 알 수 없고, 매번 실행 결괏값을 받아서 체크해줘야 한다.

try ~ catch ~ finally 문을 쓸 수 없고, if문을 복잡하게 써야 한다.

<br>

Exception을 만들어서 이를 해결할 수 있다.

과일이 없다는 의미에서 FruitNotFoundException을 생성해보겠다.

```java
package test05_user_exception;

// Exception을 상속받으면 : CheckedException
// RuntimeException을 상속받으면 : unchecked exception.

public class FruitNotFoundException extends Exception {
	
	// 생성자
	public FruitNotFoundException(String name) {
		super(name + "에 해당하는 과일은 없습니다. ㅠㅠ");
		// 문자열을 넣어서 => 에러메시지.
	}

}
```

위의 경우 Exception을 상속 받았으니, CheckedException이다. 생성자를 만들고, 과일 이름을 받아오게 하였다. 생성자가 기본 생성자 말고 문자열이 들어간 생성자가 있다. 에러 메세지를 넣어서 만들 수 있는 것이다.

<b>문자열을 넣어서 생성자를 만들면, 이것이 에러 메세지가 된다.</b>

<br>

이제 위에서 만든 FruitNotFoundException을 사용해보겠다.

```java
package test05_user_exception;

public class UserExceptionTest2 {

	public static String[] fruits;

	public static void main(String[] args) {

		fruits = new String[] { "사과", "사과", "오렌지", "토마토" };
		
		try {
			eatFruit("사과");
			eatFruit("사과");
			eatFruit("사과");
			eatFruit("사과");
			eatFruit("사과");
			eatFruit("사과");
		} catch (FruitNotFoundException e) {
			System.out.println(e.getMessage());
		}

	}

	public static void eatFruit(String name) throws FruitNotFoundException {
		for (int i = 0; i < fruits.length; i++) {
			if (name.equals(fruits[i])) {
				fruits[i] = null;
				System.out.println(name + "(을)를 먹었습니다.");
				
			}
		}
		// 사용자 정의 예외 발생
		// throw new 예외클래스생성자();
		throw new FruitNotFoundException(name); 
	}

}
```

위 코드의 출력 결과는 다음과 같다.

```
사과(을)를 먹었습니다.
사과(을)를 먹었습니다.
사과에 해당하는 과일은 없습니다. ㅠㅠ
```

<br>

정상적으로 작동하지 않았을 때 ```throw new FruitNotFoundException(name);```를 한다. 사용자 정의 예외를 발생시키는 것이다. 그런데 이 코드만 추가하면 빨간 줄이 뜬다. 이 에러가 처리되지 않았다는 것이다.

명시적으로 해줘야 한다. ```public static void eatFruit(String name) throws FruitNotFoundException {```로 하는 것이다. CheckedException이기 때문에 '나는 이 에러를 발생할 수 있는 애야'하고 알려줘야 하는 것이다. 예외 처리가 강제되고 있는 것이다.

main 메서드에서 그냥 ```eatFruit("사과");```라고 입력하면 빨간 줄이 뜬다. CheckedException이기 때문에, 처리를 해야 하는 것이다. 메인 메서드에 있는 ```eatFruit("사과");```문을 try ~ catch 문으로 감싸면 된다. 그리고 catch 문에서는 FruitNotFoundException를 반환하게 한다.

출력 결과를 보면, ```System.out.println(e.getMessage());```가 잘 출력되었음을 알 수 있다.

<br>

하나 더 보겠다. 이번에는 UncheckedException을 만들어볼 것이다.

```java
package test05_user_exception;

public class FruitNotFoundRuntimeException extends RuntimeException {
	public FruitNotFoundRuntimeException(String name) {
		super(name + "에 해당하는 과일이 없습니다.");
	}
}
```

위 경우는 RuntimeException이니 심각한 에러는 아니고, 필요할 때에 체크하라는 것이다. RuntimeException을 상속 받고 있다.

생성자를 하나 만들었다.

```java
package test05_user_exception;

public class UserExceptionTest3 {

	public static String[] fruits;

	public static void main(String[] args) {

		fruits = new String[] { "사과", "사과", "오렌지", "토마토" };
		
		try {
			eatFruit("사과");
			eatFruit("사과");
			eatFruit("사과");
			eatFruit("사과");
			eatFruit("사과");
			eatFruit("사과");
		} catch (FruitNotFoundRuntimeException e) {
			System.out.println(e.getMessage());
		}
	}

	public static void eatFruit(String name) {
		for (int i = 0; i < fruits.length; i++) {
			if (name.equals(fruits[i])) {
				fruits[i] = null;
				System.out.println(name + "(을)를 먹었습니다.");
				
			}
		}
		// 사용자 정의 예외 발생
		// throw new 예외클래스생성자();
		throw new FruitNotFoundRuntimeException(name); 
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
사과(을)를 먹었습니다.
사과(을)를 먹었습니다.
사과에 해당하는 과일이 없습니다.
```

위의 경우, RuntimeException이기 필요하면 그때 처리한다. 강제는 아닌 것이다.