---
layout: post
title: 쓰레드의 구현과 실행
description: Java의 정석 기초편 ch. 13 쓰레드
categories: Java
date: 2023-06-29 13:34:00 +0900
---
쓰레드를 구현하는 방법은 Thread클래스를 상속받는 방법과 Runnable인터페이스를 구현하는 방법, 모두 두 가지가 있다. 어느 쪽을 선택해도 별 차이는 없지만 Thread클래스를 상속받으면 다른 클래스를 상속받을 수 없기 때문에, Runnable인터페이스를 구현하는 방법이 일반적이다.

Runnable인터페이스를 구현하는 방법은 재사용성(reusability)이 높고 코드의 일관성(consistency)을 유지할 수 있기 때문에 보다 객체지향적인 방법이다.

> 1. Thread클래스를 상속
```java
class MyThread extends Thread {
    public void run() { /* 작업내용 */ }    // Thread클래스의 run()을 오버라이딩
}
```

> 2. Runnable인터페이스를 구현
```java
class MyThread implements Runnable {
    public void run() { /* 작업내용 */ }    // Runnable인터페이스의 run()을 구현
}
```

Runnable인터페이스는 오로지 run()만 정의되어 있는 간단한 인터페이스이다. Runnable인터페이스를 구현하기 위해서 해야 할 일은 추상메서드인 run()의 몸통{}을 만들어주는 것뿐이다.

```java
public interface Runnable {
    public abstract void run();
}
```

쓰레드를 구현한다는 것은 위와 같은 방법 중 어떤 것을 선택하든지, 그저 쓰레드를 통해 작업하고자 하는 내용으로 run()의 몸통{}을 채우는 것일 뿐이다.


## 쓰레드의 구현과 실행 예제

```java
class Ex13_1 {
	public static void main(String args[]) {
		ThreadEx1_1 t1 = new ThreadEx1_1();

		Runnable r = new ThreadEx1_2();
		Thread t2 = new Thread(r);      // 생성자 Thread(Runnable target)

		t1.start();
		t2.start();
	}
}

class ThreadEx1_1 extends Thread {
	public void run() {
		for(int i=0; i < 5; i++) {
			System.out.println(getName());  // 조상인 Thread의 getName()을 호출 
		}
	}
}

class ThreadEx1_2 implements Runnable {
	public void run() {
		for(int i=0; i < 5; i++) {
			// Thread.currentThread() - 현재 실행중인 Thread를 반환한다. 
			System.out.println(Thread.currentThread().getName());
		}
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Thread-0
Thread-0
Thread-0
Thread-0
Thread-0
Thread-1
Thread-1
Thread-1
Thread-1
Thread-1
```

Thread클래스를 상속받은 경우와 Runnable인터페이스를 구현한 경우의 인스턴스 생성방법이 다르다.

```java
ThreadEx1_1 t1 = new ThreadEx1_1();     // Thread의 자손 클래스의 인스턴스를 생성

Runnable r = new ThreadEx1_2();         // Runnable을 구현한 클래스의 인스턴스를 생성
Thread t2 = new Thread(r);              // 생성자 Thread(Runnable target)

Thread t2 = new Thread(new ThreadEx1_2());      // 위의 두 줄을 한 줄로 간단히
```

Runnable인터페이스를 구현한 경우, Runnable인터페이스를 구현한 클래스의 인스턴스를 생성한 다음, 이 인스턴스를 Thread클래스의 생성자의 매개변수로 제공해야 한다.

> static Thread currentThread()     현재 실행중인 쓰레드의 참조를 반환한다.\
> String getName()                  쓰레드의 이름을 반환한다.
