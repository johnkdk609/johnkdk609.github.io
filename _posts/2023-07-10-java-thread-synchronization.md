---
layout: post
title: 쓰레드의 동기화(synchronization)
description: Java의 정석 기초편 ch. 13 쓰레드
categories: Java
date: 2023-07-10 12:32:00 +0900
---
싱글쓰레드 프로세스의 경우 프로세스 내에서 단 하나의 쓰레드만 작업하기 때문에 프로세스의 자원을 가지고 작업하는 데 별 문제가 없지만, 멀티쓰레드 프로세스의 경우 여러 쓰레드가 같은 프로세스 내의 자원을 공유해서 작업하기 때문에 서로의 작업에 영향을 주게 된다. 만일 쓰레드A가 작업하던 도중에 다른 쓰레드B에게 제어권이 넘어갔을 때, 쓰레드A가 작업하던 공유데이터를 쓰레드B가 임의로 변경하였다면, 다시 쓰레드A까 제어권을 받아서 나머지 작업을 마쳤을 때 원래 의도했던 것과는 다른 결과를 얻을 수 있다.

이러한 일이 발생하는 것을 방지하기 위해서 한 쓰레드가 특정 작업을 끝마치기 전까지 다른 쓰레드에 의해 방해받지 않도록 하는 것이 필요하다. 그래서 도입된 개념이 바로 '임계 영역(critical section)'과 '잠금(락, lock)'이다.

공유 데이터를 사용하는 코드 영역을 임계 영역으로 지정해놓고, 공유 데이터(객체)가 가지고 있는 lock을 획득한 단 하나의 쓰레드만 이 영역 내의 코드를 수행할 수 있게 한다. 그리고 해당 쓰레드가 임계 영역 내의 모든 코드를 수행하고 벗어나서 lock을 반납해야만 다른 쓰레드가 반납된 lock을 획득하여 임계 영역의 코드를 수행할 수 있게 된다.

이처럼 <b>한 쓰레드가 진행 중인 작업을 다른 쓰레드가 간섭하지 못하도록 막는 것을 '쓰레드의 동기화(synchronization)'</b>라고 한다.

> 쓰레드의 동기화 - 한 쓰레드가 진행중인 작업을 다른 쓰레드가 간섭하지 못하게 막는 것

자바에서는 synchronized블럭을 이용해서 쓰레드의 동기화를 지원했지만, JDK1.5부터는 'java.util.concurrent.locks'와 'java.util.concurrent.atomic'패키지를 통해서 다양한 방식으로 동기화를 구현할 수 있도록 지원하고 있다.


## synchronized를 이용한 동기화

먼저 가장 간단한 동기화 방법인 synchronized 키워드를 이용한 동기화에 대해서 알아보자. 이 키워드는 임계 영역을 설정하는 데 사용된다. 아래와 같이 두 가지 방식이 있다.

```java
1. 메서드 전체를 임계 영역으로 지정
public synchronized void calcSum() {    //
    // ...                              //
}                                       // 임계 영역 (critical selection)

2. 특정한 영역을 임계 영역으로 지정
synchronized (객체의 참조변수) {    //
    // ...                      //
}                               // 임계 영역 (critical selection)
```

첫 번째 방법은 메서드 앞에 synchronized를 붙이는 것인데, synchronized를 붙이면 메서드 전체가 임계 영역으로 설정된다. 쓰레드는 synchronized메서드가 호출된 시점부터 해당 메서드가 포함된 객체의 lock을 얻어 작업을 수행하다가 메서드가 종료되면 lock을 반환한다.

두 번째 방법은 메서드 내의 코드 일부를 블럭{}으로 감싸고 블럭 앞에 'synchronized (참조변수)'를 붙이는 것인데, 이때 참조변수는 락(lock)을 걸고자 하는 객체를 참조하는 것이어야 한다. 이 블럭을 synchronized블럭이라고 부르며, 이 블럭의 영역 안으로 들어가면서부터 쓰레드는 지정된 객체의 lock을 얻게 되고, 이 블럭을 벗어나면 lock을 반납한다.

두 방법 모두 lock의 획득과 반납이 모두 자동적으로 이루어지므로 우리가 해야할 일은 그저 임계 영역만 설정해주는 것뿐이다.

모든 객체는 lock을 하나씩 가지고 있으며, 해당 객체의 lock을 가지고 있는 쓰레드만 임계 영역의 코드를 수행할 수 있다. 그리고 다른 쓰레드들은 lock을 얻을 때까지 기다리게 된다.

임계 영역은 멀티쓰레드 프로그램의 성능을 좌우하기 때문에 가능하면 메서드 전체에 락을 거는 것보다 syncrhonized블럭으로 임계 영역을 최소화해서 보다 효율적인 프로그램이 되도록 해야 한다.


## synchronized를 이용한 동기화 예제 1

```java
class Ex13_12 {
	public static void main(String args[]) {
		Runnable r = new RunnableEx12();
		new Thread(r).start(); // ThreadGroup에 의해 참조되므로 gc대상이 아니다. 
		new Thread(r).start(); // ThreadGroup에 의해 참조되므로 gc대상이 아니다. 
	}
}

class Account {
	private int balance = 1000;

	public  int getBalance() {
		return balance;
	}

	public void withdraw(int money){
		if (balance >= money) {
			try { Thread.sleep(1000);} catch(InterruptedException e) {}
			balance -= money;
		}
	} // withdraw
}

class RunnableEx12 implements Runnable {
	Account acc = new Account();

	public void run() {
		while (acc.getBalance() > 0) {
			// 100, 200, 300중의 한 값을 임의로 선택해서 출금(withdraw)
			int money = (int)(Math.random() * 3 + 1) * 100;
			acc.withdraw(money);
			System.out.println("balance:"+acc.getBalance());
		}
	} // run()
}
```

위 코드의 출력 결과는 다음과 같다.

```
balance:800
balance:500
balance:400
balance:300
balance:-100
balance:0
```

은행계좌(account)에서 잔고(balance)를 확인하고 임의의 금액을 출금(withdraw)하는 예제인데, 아래를 보면 잔고가 출금하려는 금액보다 큰 경우에만 출금하도록 되어 있다.

```java
public void withdraw(int money) {
    if (balance >= money) {     // 출금액(money)보다 잔고(balance)가 클 때
        try { Thread.sleep(1000); } catch (Exception e) {}
        balance -= money;
    }
}   // withdraw
```

그러나 실행 결과를 보면 잔고(balance)가 음수가 되는 것을 볼 수 있는데, 그 이유는 한 쓰레드가 if문의 조건식을 통과하고 출금하기 바로 직전에 다른 쓰레드가 끼어들어서 출금을 먼저 했기 때문이다. 


## synchronized를 이용한 동기화 예제 2

```java
class Ex13_13 {
	public static void main(String args[]) {
		Runnable r = new RunnableEx13();
		new Thread(r).start();
		new Thread(r).start();
	}
}

class Account2 {
	private int balance = 1000; // private으로 해야 동기화가 의미가 있다. 

	public  int getBalance() {
		return balance;
	}

	public synchronized void withdraw(int money){ // synchronized로 메서드를 동기화 
		if(balance >= money) {
			try { Thread.sleep(1000);} catch(InterruptedException e) {}
			balance -= money;
		}
	} // withdraw
}

class RunnableEx13 implements Runnable {
	Account2 acc = new Account2();

	public void run() {
		while(acc.getBalance() > 0) {
			// 100, 200, 300중의 한 값을 임의로 선택해서 출금(withdraw)
			int money = (int)(Math.random() * 3 + 1) * 100;
			acc.withdraw(money);
			System.out.println("balance:"+acc.getBalance());
		}
	} // run()
}
```

위 코드의 출력 결과는 다음과 같다.

```
balance:900
balance:600
balance:300
balance:100
balance:0
balance:0
```

이전 예제의 withdraw()에 그저 synchronized만 붙였을 뿐인데도, 전과 달리 결과에 음수 값이 나타나지 않는 것을 확인할 수 있다. 여기서 한 가지 주의할 점은 Account클래스의 인스턴스변수인 balance의 접근 제어자가 private이라는 것이다. 만일 private이 아니면, 외부에서 직접 접근할 수 있기 때문에 아무리 동기화를 해도 이 값의 변경을 막을 길이 없다. synchronized를 이용한 동기화는 지정된 영역의 코드를 한 번에 하나의 쓰레드가 수행하는 것을 보장하는 것일 뿐이기 때문이다.