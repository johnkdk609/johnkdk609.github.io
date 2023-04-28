---
layout: post
title: 사용자 정의 예외(Custom Exception) 만들기
description: Java의 정석 기초편 ch. 8 예외처리
categories: Java
date: 2023-04-28 18:04:00 +09:00
---
기존에 정의된 예외 클래스 외에 필요에 따라 프로그래머가 새로운 예외 클래스를 정의하여 사용할 수 있다. 보통 Exception클래스 또는 RuntimeException클래스로부터 상속받는 클래스를 만들지만, 필요에 따라서 알맞은 예외 클래스를 선택할 수 있다. 

```java
class MyException extends Exception {
  MyException(String msg) {   // 문자열을 매개변수로 받는 생성자
    super(msg);   // 조상인 Exception클래스의 생성자를 호출한다.
  }
}
```

Exception클래스로부터 상속받아서 MyException클래스를 만들었다. 필요하다면, 멤버변수나 메서드를 추가할 수 있다. Exception클래스는 생성 시에 String값을 받아서 메시지로 저장할 수 있다. 우리가 만든 사용자 정의 예외 클래스도 메시지를 지정할 수 있으려면, 위에서 보는 것과 같이 String을 매개변수로 받는 생성자를 추가해주어야 한다.

```java
class MyException extends Exception {
  // 에러 코드 값을 저장하기 위한 필드를 추가했다.
  private final int ERR_CODE;   // 생성자를 통해 초기화 한다.
  
  MyException(String msg, int errCode) {  // 생성자
    super(msg);
    ERR_CODE = errCode;
  }
  
  MyException(String msg) {   // 생성자
    this(msg, 100);           // ERR_CODE를 100(기본값)으로 초기화한다.
  }
  
  public int getErrCode() {   // 에러 코드를 얻을 수 있는 메서드도 추가했다.
    return ERR_CODE;    // 이 메서드는 주로 getMessage()와 함께 사용될 것이다.
  }
}
```

이전의 코드를 좀더 개선하여 메시지뿐만 아니라 에러코드 값도 저장할 수 있도록 ERR_CODE와 getErrCode()를 MyException클래스의 멤버로 추가했다. 이렇게 함으로써 MyException이 발생했을 때, catch블럭에서 getMessage()와 getErrCode()를 사용해서 에러코드와 메시지를 모두 얻을 수 있을 것이다. 

기존의 예외 클래스는 주로 Exception을 상속받아서 'checked 예외'로 작성하는 경우가 많았지만, 요즘은 예외처리를 선택적으로 할 수 있도록 RuntimeException을 상속받아서 작성하는 쪽으로 바뀌어 가고 있다. 'checked 예외'는 반드시 예외처리를 해주어야 하기 때문에 예외처리가 불필요한 경우에도 try-catch문을 넣어서 코드가 복잡해지기 때문이다. 


### 사용자 정의 예외 만들기 예제

```java
class Ex8_11 {
  public static void main(String args[]) {
    try {
      startInstall();       // 프로그램 설치에 필요한 준비를 한다. 
      copyFiles();          // 파일들을 복사한다. 
    } catch (SpaceException e)	{
      System.out.println("에러 메시지 : " + e.getMessage());
      e.printStackTrace();
      System.out.println("공간을 확보한 후에 다시 설치하시기 바랍니다.");
    } catch (MemoryException me)	{
      System.out.println("에러 메시지 : " + me.getMessage());
      me.printStackTrace();
      System.gc();          // Garbage Collection을 수행하여 메모리를 늘려준다. 
      System.out.println("다시 설치를 시도하세요.");
    } finally {
      deleteTempFiles();    // 프로그램 설치에 사용된 임시파일들을 삭제한다. 
    } // try의 끝 
  } // main의 끝 

  static void startInstall() throws SpaceException, MemoryException { 
    if(!enoughSpace())      // 충분한 설치 공간이 없으면...
      throw new SpaceException("설치할 공간이 부족합니다.");
    if (!enoughMemory())    // 충분한 메모리가 없으면...
      throw new MemoryException("메모리가 부족합니다.");
    } // startInstall메서드의 끝 

  static void copyFiles() { /* 파일들을 복사하는 코드를 적는다. */ }
  static void deleteTempFiles() { /* 임시파일들을 삭제하는 코드를 적는다. */ }
   
  static boolean enoughSpace() {
		// 설치하는 데에 필요한 공간이 있는지 확인하는 코드를 적는다. 
    return false;
  }
   
  static boolean enoughMemory() {
    // 설치하는 데에 필요한 메모리 공간이 있는지 확인하는 코드를 적는다. 
    return true;
  }
} // ExceptionTest클래스의 끝 

class SpaceException extends Exception {
  SpaceException(String msg) {
    super(msg);	
  }
}

class MemoryException extends Exception {
  MemoryException(String msg) {
    super(msg);	
  }
}
```

위 코드의 출력 결과는 다음과 같다.

```java
에러 메시지 : 설치할 공간이 부족합니다.
SpaceException: 설치할 공간이 부족합니다.
	at Ex8_11.startInstall(Ex8_11.java:22)
	at Ex8_11.main(Ex8_11.java:4)
공간을 확보한 후에 다시 설치하시기 바랍니다.
```

MemoryException과 SpaceException, 이 두 개의 사용자 정의 예외 클래스를 새로 만들어서 사용했다. SpaceException은 프로그램을 설치하려는 곳에 충분한 공간이 없을 경우에 발생하도록 했으며, MemoryException은 설치 작업을 수행하는 데에 메모리가 충분히 확보되지 않았을 경우에 발생하도록 하였다. 

이 두 예외는 startInstall()을 수행하는 동안에 발생할 수 있으며, enoughSpace()와 enoughMemory()의 실행 결과에 따라서 발생하는 예외의 종류가 달라지도록 했다.

이번 예제에서 enoughSpace()와 enoughMemory()는 단순히 false와 true를 각각 반환하도록 되어 있지만 설치 공간과 사용 가능한 메모리를 확인하는 기능을 한다고 가정하였다.
