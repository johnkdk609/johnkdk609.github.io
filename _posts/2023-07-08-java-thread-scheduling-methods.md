---
layout: post
title: 쓰레드의 실행제어
description: Java의 정석 기초편 ch. 13 쓰레드
categories: Java
date: 2023-07-08 23:28:00 +0900
---
쓰레드 프로그래밍이 어려운 이유는 동기화(synchronization)와 스케줄링(scheduling) 때문이다. 앞서 우선순위를 통해 쓰레드간의 스케줄링을 하는 방법을 공부하기는 했지만, 이것만으로는 한참 부족하다. 효율적인 멀티쓰레드 프로그램을 만들기 위해서는 보다 정교한 스케줄링을 통해 프로세스에게 주어진 자원과 시간을 여러 쓰레드가 낭비없이 잘 사용하도록 프로그래밍 해야 한다.

쓰레드의 스케줄링을 잘하기 위해서는 쓰레드의 상태와 관련 메서드를 잘 알아야 하는데, 먼저 쓰레드의 스케줄링과 관련된 메서드는 다음과 같다.

<table>
    <tr>
        <th>메서드</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>static void sleep(long millis)<br>static void sleep(long millis, int nanos)</td>
        <td>지정된 시간(천분의 일초 단위)동안 쓰레드를 일시정지시킨다. 지정한 시간이 지나고 나면, 자동적으로 다시 실행대기상태가 된다.</td>
    </tr>
    <tr>
        <td>void join()<br>void join(long millis)<br>void join(long millis, int nanos)</td>
        <td>지정된 시간동안 쓰레드가 실행되도록 한다. 지정된 시간이 지나거나 작업이 종료되면 join()을 호출한 쓰레드로 다시 돌아와 실행을 계속한다.</td>
    </tr>
    <tr>
        <td>void interrupt()</td>
        <td>sleep()이나 join()에 의해 일시정지상태인 쓰레드를 깨워서 실행대기상태로 만든다. 해당 쓰레드에서는 InterruptedException이 발생함으로써 일시정지상태를 벗어나게 된다.</td>
    </tr>
    <tr>
        <td>void stop()</td>
        <td>쓰레드를 즉시 종료시킨다.</td>
    </tr>
    <tr>
        <td>void suspend()</td>
        <td>쓰레드를 일시정지시킨다. resume()을 호출하면 다시 실행대기상태가 된다.</td>
    </tr>
    <tr>
        <td>void resume()</td>
        <td>suspend()에 의해 일시정지상태에 있는 쓰레드를 실행대기상태로 만든다.</td>
    </tr>
    <tr>
        <td>static void yield()</td>
        <td>실행 중에 자신에게 주어진 실행시간을 다른 쓰레드에게 양보(yield)하고 자신은 실행대기상태가 된다.</td>
    </tr>
</table>

위 표는 쓰레드의 스케줄링과 관련한 메서드들이다.

(resume(), stop(), suspend()는 쓰레드를 교착상태(dead-lock)로 만들기 쉽기 때문에 deprecated 되었다.)


## sleep()

sleep()은 지정된 시간동안 쓰레드를 멈추게 한다.

```java
static void sleep(long millis)
static void sleep(long millis, int nanos)
```

밀리세컨드(millis, 1000분의 일초)와 나노세컨드(nanos, 10억분의 일초)의 시간단위로 세밀하게 값을 지정할 수 있지만 어느 정도의 오차가 발생할 수 있다는 것을 염두에 두어야 한다. 예를 들어, 쓰레드가 0.0015초 동안 멈추게 하려면 다음과 같이 해야 한다.

(나노세컨드(nanos)로 지정할 수 있는 값의 범위는 0~999999이며, 999999나노세컨드는 약 1밀리세컨드이다.)

```java
try {
    Thread.sleep(1, 500000);    // 쓰레드를 0.0015초 동안 멈추게 한다.
} catch(InterruptedException e) {}
```

sleep()에 의해 일시정지 상태가 된 쓰레드는 지정된 시간이 다 되거나 interrupt()가 호출되면, InterruptedException이 발생되어 잠에서 깨어나 실행대기 상태가 된다.

그래서 sleep()을 호출할 때는 항상 try-catch문으로 예외를 처리해줘야 한다. 매번 예외처리를 해주는 것이 번거롭기 때문에, 아래와 같이 try-catch문까지 포함하는 새로운 메서드를 만들어서 사용하기도 한다.

```java
void delay(long millis) {
    try {
        Thread.sleep(millis);
    } catch(InterruptedException e) {}
}
```


## sleep() 예제

```java
class Ex13_8 {
	public static void main(String args[]) {
		ThreadEx8_1 th1 = new ThreadEx8_1();
		ThreadEx8_2 th2 = new ThreadEx8_2();
		th1.start(); th2.start();

		try {
			th1.sleep(2000);	
		} catch(InterruptedException e) {}

		System.out.print("<<main 종료>>");
	} // main
}

class ThreadEx8_1 extends Thread {
	public void run() {
		for(int i=0; i < 300; i++) System.out.print("-");
		System.out.print("<<th1 종료>>");
	} // run()
}

class ThreadEx8_2 extends Thread {
	public void run() {
		for(int i=0; i < 300; i++) System.out.print("|");
		System.out.print("<<th2 종료>>");
	} // run()
}
```

위 코드의 출력 결과는 다음과 같다.

```
----------------------------------------------------------------------------------|||||||||||||||---------------------||||---------------------------------------------------------------------|||||||||||||||||||||||||||||||||||---------------------------------------------------------|||||||||||||||||-----------|||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||-------------|||||||||||||||||||||||||||||||||||||||||||||||||||----------------||||--|-----------|-|--|-||||||||||||||||||||||||||||||-||--|||||||||||||||||||-----------<<th1 종료>><<th2 종료>><<main 종료>>
```

위의 결과를 보면 쓰레드 th1의 작업이 가장 먼저 종료되었고, 그 다음이 th2, main의 순인 것을 알 수 있다. 그러나 아래의 코드를 생각해보면 이 결과가 뜻밖이라는 생각이 들 것이다.

```java
try {
    th1.sleep(2000);    // th1을 2초동안 멈추게? Thread.sleep(2000);이 바람직
} catch(InterruptException e) {}
```

왜냐하면 sleep()는 항상 현재 실행중인 쓰레드에 대해 작동하기 때문에 'th1.sleep(2000)'과 같이 호출되었어도 실제로 영향을 받는 것은 main메서드를 실행하는 main쓰레드이기 때문이다.


## interrupt()

진행 중인 쓰레드의 작업이 끝나기 전에 취소시켜야 할 때가 있다. 예를 들어 큰 파일을 다운로드받을 때 시간이 너무 오래 걸리면 중간에 다운로드를 포기하고 취소할 수 있어야 한다. 

interrupt()는 쓰레드에게 작업을 멈추라고 요청한다. 단지 멈추라고 요청만 하는 것일 뿐 쓰레드를 강제로 종료시키지는 못한다. interrupt()는 그저 쓰레드의 interrupted상태(인스턴스 변수)를 바꾸는 것일 뿐이다.

그리고 interrupted()는 쓰레드에 대해 interrupt()가 호출되었는지 알려준다. interrupt()가 호출되지 않았다면 false를, interrupt()가 호출되었다면 true를 반환한다.

```java
Thread th = new Thread();
th.start();
    ...
th.interrupt();     // 쓰레드 th에 interrupt()를 호출한다.
    ...
class MyThread extends Thread {
    public void run() {
        while (!interrupted()) {    // interrupted()의 결과가 false인 동안 반복
            ...
        }
    }
}
```

interrupt()가 호출되면, interrupted()의 결과가 false에서 true로 바뀌어 while문을 벗어나게 된다. while문의 조건식에 '!'가 포함되어 있는 것에 주의해야 한다.

isInterrupted()도 쓰레드의 interrupt()가 호출되었는지 확인하는 데 사용할 수 있지만, interrupted()와 달리 isInterrupted()는 쓰레드의 interrupted상태를 false로 초기화하지 않는다.

```java
void interrupt()    // 쓰레드의 interrupted상태를 false에서 true로 변경
boolean isInterrupted()     // 쓰레드의 interrupted상태를 반환
static boolean interrupted()    // 현재 쓰레드의 interrupted상태를 반환 후, false로 변경
```


## interrupt() 예제

```java
import javax.swing.JOptionPane;

class Ex13_9 {
	public static void main(String[] args) throws Exception {
		ThreadEx9_1 th1 = new ThreadEx9_1();
		th1.start();

		String input = JOptionPane.showInputDialog("아무 값이나 입력하세요."); 
		System.out.println("입력하신 값은 " + input + "입니다.");
		th1.interrupt();  // interrupt()를 호출하면, interrupted상태가 true가 된다. 
		System.out.println("isInterrupted():"+ th1.isInterrupted()); // true
	}
}

class ThreadEx9_1 extends Thread {
	public void run() {
		int i = 10;

		while(i!=0 && !isInterrupted()) {
			System.out.println(i--);
			for(long x=0;x<2500000000L;x++); // 시간 지연 
		}
		System.out.println("카운트가 종료되었습니다.");
	} 
}
```

위 코드를 실행시키면, 출력이 되는 도중 '아무 값이나 입력하세요.'라는 창이 나타난다. 거기에 'abcd'를 입력하였다. 그러면 다음과 같이 출력된다.

```
10
9
8
7
6
5
4
3
입력하신 값은 abcd입니다.
isInterrupted():true
카운트가 종료되었습니다.
```


## suspend(), resume(), stop()

suspend()는 sleep()처럼 쓰레드를 멈추게 한다. suspend()에 의해 정지된 쓰레드는 resume()을 호출해야 다시 실행대기 상태가 된다. stop()은 호출되는 즉시 쓰레드가 종료된다.

suspend(), resume(), stop()은 쓰레드의 실행을 제어하는 가장 손쉬운 방법이지만, suspend()와 stop()이 교착상태(deadlock)를 일으키기 쉽게 작성되었으므로 사용이 권장되지 않는다. 그래서 이 메서드들은 모두 'deprecated'되었다. Java API문서 stop()을 찾아보면 아래와 같이 'Deprecated.'라고 적혀있다.

<img width="1338" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/765f7409-8954-4cd4-bc56-f09054cfc124">

'deprecated'의 의미는 '전에는 사용되었지만, 앞으로 사용하지 않을 것을 권장한다.'이다. 'deprecated'된 메서드는 하위 호환성을 위해서 삭제하지 않는 것일 뿐이므로 사용해서는 안 된다.


## suspend(), resume(), stop() 예제

```java
class Ex13_10 {
	public static void main(String args[]) {
		RunImplEx10 r = new RunImplEx10();
		Thread th1 = new Thread(r, "*");
		Thread th2 = new Thread(r, "**");
		Thread th3 = new Thread(r, "***");
		th1.start();
		th2.start();
		th3.start();

		try {
			Thread.sleep(2000);
			th1.suspend();	// 쓰레드 th1을 잠시 중단시킨다. 
			Thread.sleep(2000);
			th2.suspend();
			Thread.sleep(3000);
			th1.resume();	// 쓰레드 th1이 다시 동작하도록 한다. 
			Thread.sleep(3000);
			th1.stop();		// 쓰레드 th1을 강제종료시킨다. 
			th2.stop();
			Thread.sleep(2000);
			th3.stop();
		} catch (InterruptedException e) {}
	} // main
}

class RunImplEx10 implements Runnable {
	public void run() {
		while(true) {
			System.out.println(Thread.currentThread().getName());
			try {
				Thread.sleep(1000);
			} catch(InterruptedException e) {}
		}
	} // run()
}
```

위 코드를 실행시키면 다음과 같은 결과가 나온다. 실행환경과 상황에 따라 실행결과가 달라질 수 있다.

```
*
***
**
*
***
**
***
**
***
**
***
***
***
*
***
*
***
*
***
**
***
***
```


## join()과 yield()

### join() - 다른 쓰레드의 작업을 기다린다.

쓰레드 자신이 하던 작업을 잠시 멈추고 다른 쓰레드가 지정된 시간동안 작업을 수행하도록 할 때 join()을 사용한다.

```java
void join()
void join(long millis)
void join(long millis, int nanos)
```

시간을 지정하지 않으면, 해당 쓰레드가 작업을 모두 마칠 때까지 기다리게 된다. 작업 중에 다른 쓰레드의 작업이 먼저 수행되어야할 필요가 있을 때 join()을 사용한다.

```java
try {
    th1.join();     // 현재 수행중인 쓰레드가 쓰레드 th1의 작업이 끝날 때까지 기다린다.
} catch (InterruptedException e) {}
```

join()도 sleep()처럼 interrupt()에 의해 대기상태에서 벗어날 수 있으며, join()이 호출되는 부분을 try-catch문으로 감싸야 한다. join()은 여러모로 sleep()과 유사한 점이 많은데, sleep()과 다른 점은 join()은 현재 쓰레드가 아닌 특정 쓰레드에 대해 동작하므로 static메서드가 아니라는 것이다.

(join()은 자신의 작업 중간에 다른 쓰레드의 작업을 참여(join)시킨다는 의미로 이름 지어진 것이다.)


### yield() - 다른 쓰레드에게 양보한다.

yield()는 쓰레드 자신에게 주어진 실행시간을 다음 차례의 쓰레드에게 양보(yield)한다.

예를 들어 스케줄러에 의해 1초의 실행시간을 할당받은 쓰레드가 0.5초의 시간동안 작업한 상태에서 yield()가 호출되면, 나머지 0.5초는 포기하고 다시 실행대기상태가 된다.

yield()와 interrupt()를 적절히 사용하면, 프로그램의 응답성을 높이고 보다 효율적인 실행이 가능하게 할 수 있다.


## join()과 yield() 예제

```java
class Ex13_11 {
	static long startTime = 0;

	public static void main(String args[]) {
		ThreadEx11_1 th1 = new ThreadEx11_1();
		ThreadEx11_2 th2 = new ThreadEx11_2();
		th1.start();
		th2.start();
		startTime = System.currentTimeMillis();

		try {
			th1.join();	// main쓰레드가 th1의 작업이 끝날 때까지 기다린다. 
			th2.join();	// main쓰레드가 th2의 작업이 끝날 때까지 기다린다. 
		} catch(InterruptedException e) {}

		System.out.print("소요시간:" + (System.currentTimeMillis() - Ex13_11.startTime));
	} // main
}

class ThreadEx11_1 extends Thread {
	public void run() {
		for(int i=0; i < 300; i++) {
			System.out.print(new String("-"));
		}
	} // run()
}

class ThreadEx11_2 extends Thread {
	public void run() {
		for(int i=0; i < 300; i++) {
			System.out.print(new String("|"));
		}
	} // run()
}
```

위 코드의 실행 결과는 다음과 같다.

```
-----------||||||||||||||||||---|||||||||||||||||||||||||||||||||||||||||||||||||||||||||-------------------------------------------------------------------|||||||||||||-----------------|||||||||||||||||||||||||||||||||||---------||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||-------------------------------------------------------------------------|-||||||||||||||||-----------------------------------------------|||||||||||||||||||||||||||||||||||||||||----------------------|||||||||||||||||--------------------------------------------------소요시간:10
```

join()을 사용하지 않았으면 main쓰레드는 바로 종료되었겠지만, join()으로 쓰레드 th1과 th2의 작업을 마칠 때까지 main쓰레드가 기다리도록 했다. 그래서 main쓰레드가 두 쓰레드의 작업에 소요된 시간을 출력할 수 있다.