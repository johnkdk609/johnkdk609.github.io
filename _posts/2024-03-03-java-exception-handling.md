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