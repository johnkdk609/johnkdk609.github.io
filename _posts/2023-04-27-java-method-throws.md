---
layout: post
title: 메서드에 예외 선언하기 - throws
description: Java의 정석 기초편 ch. 8 예외처리
categories: Java
date: 2023-04-27 11:02:00 +09:00
---
예외를 선언하는 방법에는 지금까지 배워 온 try-catch문을 사용하는 것 외에, 예외를 메서드에 선언하는 방법이 있다.

메서드에 예외를 선언하려면, 메서드의 선언부에 키워드 **throws**를 사용해서 메서드 내에서 발생할 수 있는 예외를 적어주기만 하면 된다. 그리고 예외가 여러 개일 경우에는 쉼표(,)로 구분한다.

```java
void method() throws Exception1, Exception2, ... ExceptionN {
  // 메서드의 내용
}
```

(예외를 발생시키는 키워드 throw와 예외를 메서드에 선언할 때 쓰이는 throws를 잘 구분해야 한다.)

만일 아래와 같이 예외의 최고 조상인 Exception 클래스를 메서드에 선언하면, 이 메서드는 모든 종류의 예외가 발생할 가능성이 있다는 뜻이다.

```java
void method() throws Exception {
  // 메서드의 내용
}
```

예외를 선언하면, 이 예외뿐만 아니라 그 자손 타입의 예외까지도 발생할 수 있다는 점에 주의해야 한다. 앞서 오버라이딩에서 살펴본 것과 같이, 오버라이딩할 때는 단순히 선언된 예외의 개수가 아니라 상속 관계까지 고려해야 한다. 

메서드의 선언부에 예외를 선언함으로써 메서드를 사용하려는 사람이 메서드의 선언부를 보았을 때, 이 메서드를 사용하기 위해서는 어떠한 예외들이 처리되어져야 하는지 쉽게 알 수 있다.

기존의 많은 언어들에서는 메서드에 예외 선언을 하지 않기 때문에, 경험 많은 프로그래머가 아니고서는 어떤 상황에 어떤 종류의 예외가 발생할 가능성이 있는지 충분히 예측하기 힘들기 때문에 그에 대한 대비를 하는 것이 어려웠다. 

그러나 자바에서는 메서드를 작성할 때 메서드 내에서 발생할 가능성이 있는 예외를 메서드의 선언부에 명시하여 이 메서드를 사용하는 쪽에서는 이에 대한 처리를 하도록 강요하기 때문에, 프로그래머들의 짐을 덜어주는 것은 물론이고 보다 견고한 프로그램 코드를 작성할 수 있도록 도와준다. 


### 메서드에 예외 선언하기 예제 1

```java
class Ex8_9 {
	public static void main(String[] args) throws Exception {
		method1();  // 같은 클래스 내의 static 멤버이므로 객체 생성 없이 직접 호출 가능 
  	} // main 메서드의 끝 

	static void method1() throws Exception {
		method2();
	} // method1의 끝 

	static void method2() throws Exception {
		throw new Exception();
	} // method2의 끝 
}
```

위 코드의 출력 결과는 다음과 같다.

```
Exception in thread "main" java.lang.Exception
	at Ex8_9.method2(Ex8_9.java:11)
	at Ex8_9.method1(Ex8_9.java:7)
	at Ex8_9.main(Ex8_9.java:3)
```

위의 실행 결과를 보면, 프로그램의 실행 도중 java.lang.Exception이 발생하여 비정상적으로 종료했다는 것과 예외가 발생했을 때 호출스택(call stack)의 내용을 알 수 있다.

위의 결과로부터 다음과 같은 사실을 알 수 있다.

> 1. 예외가 발생했을 때, 모두 3개의 메서드(main, method1, method2) 가 호출스택에 있었으며
> 2. 예외가 발생한 곳은 제일 윗줄에 있는 method2()라는 것과
> 3. main 메서드가 method1()을, 그리고 method1()은 method2()를 호출했다는 것을 알 수 있다.

위의 예제를 보면, method2()에서 'throw new Exception();' 문장에 의해 예외가 강제적으로 발생했으나 try-catch문으로 예외처리를 해주지 않았으므로, method2()는 종료되면서 예외를 자신을 호출한 method1()에게 넘겨준다. method1()에서도 역시 예외처리를 해주지 않았으므로 main 메서드에게 예외를 넘겨준다. 

그러나 main 메서드에서조차 예외처리를 해주지 않았으므로 main 메서드가 종료되어 프로그램이 예외로 인해 비정상적으로 종료되는 것이다.

**이처럼 예외가 발생한 메서드에서 예외처리를 하지 않고 자신을 호출한 메서드에게 예외를 넘겨줄 수는 있지만, 이것으로 예외가 처리된 것은 아니고 예외를 단순히 전달만 하는 것이다. 결국 어느 한 곳에서는 반드시 try-catch문으로 예외처리를 해주어야 한다.**

```java
public static void main(String[] args) throws Exception {
  method1();  // 같은 클래스 내의 static 멤버이므로 객체 생성 없이 직접 호출 가능
}
```

예외가 선언되어 있으면 Exception과 같은 체크드(checked) 예외를 try-catch문으로 처리하지 않아도 컴파일 에러가 발생하지 않는다.


### 메서드에 예외 선언하기 예제 2

```java
import java.io.*;

class Ex8_10 {
	public static void main(String[] args) {
		try {
			File f = createFile(args[0]);
			System.out.println(f.getName() + "파일이 성공적으로 생성되었습니다.");
		} catch (Exception e) {
			System.out.println(e.getMessage() + " 다시 입력해 주시기 바랍니다.");
		}
	} // main 메서드의 끝 

	static File createFile(String fileName) throws Exception {
		if (fileName==null || fileName.equals(""))
			throw new Exception("파일 이름이 유효하지 않습니다.");
		File f = new File(fileName);  //  File 클래스의 객체를 만든다. 
     	        // File 객체의 createNewFile 메서드를 이용해서 실제 파일을 생성한다. 
		f.createNewFile();
		return f; // 생성된 객체의 참조를 반환한다. 
	} // createFile 메서드의 끝 
} // 클래스의 끝 
```

위 코드에서 Run Configurations -> Arguments -> Program Arguments 항목에 아무 것도 기입하지 않거나 ""라 입력하고 출력하면,

```
파일 이름이 유효하지 않습니다. 다시 입력해 주시기 바랍니다.
```

이렇게 출력된다. 반면, Program Arguments 항목에 가령 test2.txt라고 기입하고 출력하면,

```
test2.txt파일이 성공적으로 생성되었습니다.
```

이렇게 출력되고 같은 패키지에 test2.txt 파일이 생성된다.

위 예제는 사용자로부터 파일 이름을 입력 받아서 파일을 생성하는 예제이다. 파일을 생성하는 것은 createFile()인데, 이 메서드는 입력 받은 파일의 이름이 유효하지 않으면 예외를 발생시킨다. createFile()에 예외가 선언되어 있으므로, 이 예외는 main()으로 전달되고 main()의 try-catch문에 의해 처리된다. 

이는 main()이 createFile()에게 파일을 생성하라고 명령했는데, 작업을 수행하는 과정에서 문제가 생기자 예외를 발생시켜서 main()에게 알리는 것이라고 이해할 수 있다. 

반대로 createFile()에 try-catch를 넣으면, 문제(예외)가 발생했을 때 createFile()이 예외를 처리하기 때문에 main()은 예외가 발생한 사실조차 모르게 된다.

이처럼 **예외가 발생했을 때, 예외가 발생한 메서드 내에서 자체적으로 처리해도 되는 경우 메서드 내에 try-catch문을 넣어서 처리하고**, 위 예제처럼 **메서드 내에서 자체적으로 해결이 안 되는 경우(파일 이름을 다시 받아와야 하는 경우)에는 예외를 선언해서, 호출한 메서드가 처리하도록 해야 한다.**
