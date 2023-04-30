---
layout: post
title: 연결된 예외(chained exception)
description: Java의 정석 기초편 ch. 8 예외처리
categories: Java
date: 2023-04-30 11:56:00 +09:00
---
한 예외가 다른 예외를 발생시킬 수도 있다. 예를 들어 예외 A가 예외 B를 발생시켰다면, A를 B의 '**원인 예외(cause exception)**'라고 한다.

아래의 코드는 예제8-11의 일부를 변경한 것으로, SpaceException을 원인 예외로 하는 InstallException을 발생시키는 방법을 보여준다.

```java
try {
  startInstall();     // SpaceException 발생
  copyFiles();
} catch (SpaceException e) {
  InstallException ie = new InstallException("설치 중 예외 발생");   // 예외 생성
  ie.initCause(e);    // InstallException의 원인 예외를 SpaceException으로 지정
  throw ie;           // InstallException을 발생시킨다.
} catch (MemoryException me) {
  // ...
```

먼저 InstallException을 생성한 후에, initCause()로 SpaceException을 InstallException의 원인 예외로 등록한다. 그리고 'throw'로 이 예외를 던진다.

initCause()는 Exception클래스의 조상인 Throwable클래스에 정의되어 있기 때문에 모든 예외에서 사용 가능하다.

> **Throwable initCause(Throwable cause)** - 지정한 예외를 원인 예외로 등록\
> **Throwable getCause()** - 원인 예외를 반환

발생한 예외를 그냥 처리하면 될 텐데, 원인 예외로 등록해서 다시 예외를 발생시키는지 궁금할 것이다. 그 이유는 여러 가지 예외를 하나의 큰 분류의 예외로 묶어서 다루기 위해서이다.

그렇다고 아래와 같이 InstallException을 SpaceException과 MemoryException의 조상으로 해서 catch블럭을 작성하면, 실제로 발생한 예외가 어떤 것인지 알 수 없다는 문제가 생긴다. 그리고 SpaceException과 MemoryException의 상속 관계를 변경해야 한다는 것도 부담이다. 

```java
try {
  startInstall();                 // SpaceException 발생
  copyFiles();
} catch (InstallException e) {    // InstallException은
  e.printStackTrace();            // SpaceException과 MemoryException의 조상
}
```

그래서 생각한 것이 **예외가 원인 예외를 포함할 수 있게 한 것**이다. 이렇게 하면, **두 예외는 상속 관계가 아니어도 상관없다**. 

```java
public class Throwable implements Serializable {
  ...
  private Throwable cause = this;   // 객체 자신(this)을 원인 예외로 등록
  ...
}
```

또 다른 이유는 checked 예외를 unchecked 예외로 바꿀 수 있도록 하기 위해서이다. checked 예외로 예외처리를 강제한 이유는 프로그래밍 경험이 적은 사람도 보다 견고한 프로그램을 작성할 수 있도록 유도하기 위한 것이었는데, 지금은 자바가 처음 개발되던 1990년대와 컴퓨터 환경이 많이 달라졌다. 그래서 checked 예외가 발생해도 예외를 처리할 수 없는 상황이 하나 둘 발생하기 시작했다. 이럴 때 할 수 있는 일이라곤 그저 의미 없는 try-catch문을 추가하는 것뿐인데, checked 예외를 unchecked 예외로 바꾸면 예외처리가 선택적이 되므로 억지로 예외처리를 하지 않아도 된다.

```java
static void startInstall() throws SpaceException, MemoryException {
  if (!enoughSpace())   // 충분한 설치 공간이 없으면...
    throw new SpaceException("설치할 공간이 부족합니다.");
    
  if (!enoughMemory())  // 충분한 메모리가 없으면...
    throw new MemoryException("메모리가 부족합니다.");
}
```

위 코드를 아래와 같이 변경할 수 있는 것이다.

```java
static void startInstall() throws SpaceException {
  if (!enoughSpace())   // 충분한 설치 공간이 없으면...
    throw new SpaceException("설치할 공간이 부족합니다.");
    
  if (!enoughMemory())  // 충분한 메모리가 없으면...
    throw new RuntimeException(new MemoryException("메모리가 부족합니다."));
}   // startInstall메서드의 끝
```

MemoryException은 Exception의 자손이므로 반드시 예외를 처리해야 하는데, 이 예외를 RuntimeException으로 감싸버렸기 때문에 unchecked예외가 되었다. 그래서 더 이상 startInstall()의 선언부에 MemoryException을 선언하지 않아도 된다. 참고로 위의 코드에서는 initCause() 대신 RuntimeException의 생성자를 사용했다.

```java
RuntimeException(Throwable cause)   // 원인 예외를 등록하는 생성자
```


### 연결된 예외(chained exception) 예제 코드

```java
class Ex8_13 {
	public static void main(String args[]) {
		try {
			install();
		} catch(InstallException e) {
			e.printStackTrace();
		} catch(Exception e) {
			e.printStackTrace();		
		}
	} // main의 끝 

	static void install() throws InstallException {
		try {
			startInstall();		// 프로그램 설치에 필요한 준비를 한다. 
			copyFiles();		// 파일들을 복사한다. 
		} catch (SpaceException2 e) {
			InstallException ie = new InstallException("설치 중 예외 발생");
			ie.initCause(e);
			throw ie;
		} catch (MemoryException2 me) {
			InstallException ie = new InstallException("설치 중 예외 발생");
			ie.initCause(me);
			throw ie;
		} finally {
			deleteTempFiles();    // 프로그램 설치에 사용된 임시 파일들을 삭제한다. 
		} // try의 끝 
	}

	static void startInstall() throws SpaceException2, MemoryException2 { 
		if(!enoughSpace()) {    // 충분한 설치 공간이 없으면... 
			throw new SpaceException2("설치할 공간이 부족합니다.");
		}

		if (!enoughMemory()) {  // 충분한 메모리가 없으면... 
			throw new MemoryException2("메모리가 부족합니다.");
//			throw new RuntimeException(new MemoryException("메모리가 부족합니다."));
		}
	} // startInstall메서드의 끝 

   static void copyFiles()       { /* 파일들을 복사하는 코드를 적는다. */ }
   static void deleteTempFiles() { /* 임시 파일들을 삭제하는 코드를 적는다. */ }
   
   static boolean enoughSpace() {
		// 설치하는 데에 필요한 공간이 있는지 확인하는 코드를 적는다.
		return false;
   }
   static boolean enoughMemory() {
		// 설치하는 데에 필요한 메모리 공간이 있는지 확인하는 코드를 적는다. 
		return true;
   }
} // ExceptionTest클래스의 끝 

class InstallException extends Exception {
	InstallException(String msg) {
	   super(msg);	
   }
} 

class SpaceException2 extends Exception {
	SpaceException2(String msg) {
	   super(msg);	
   }
} 

class MemoryException2 extends Exception {
	MemoryException2(String msg) {
	   super(msg);	
   }
}
```

위 코드의 출력 결과는 다음과 같다.

```
InstallException: 설치 중 예외 발생
	at Ex8_13.install(Ex8_13.java:17)
	at Ex8_13.main(Ex8_13.java:4)
Caused by: SpaceException2: 설치할 공간이 부족합니다.
	at Ex8_13.startInstall(Ex8_13.java:31)
	at Ex8_13.install(Ex8_13.java:14)
	... 1 more
```
