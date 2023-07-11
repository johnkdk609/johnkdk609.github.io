---
layout: post
title: wait()과 notify()
description: Java의 정석 기초편 ch. 13 쓰레드
categories: Java
date: 2023-07-11 10:10:00 +0900
---
synchronized로 동기화해서 공유 데이터를 보호하는 것까지는 좋은데, 특정 쓰레드가 객체의 락을 가진 상태로 오랜 시간을 보내지 않도록 하는 것이 중요하다. 만일 계좌에 출금할 돈이 부족해서 한 쓰레드가 락을 보유한 채로 돈이 입금될 때까지 오랜 시간을 보낸다면, 다른 쓰레드들은 모두 해당 객체의 락을 기다리느라 다른 작업들도 원활히 진행되지 않을 것이다.

이러한 상황을 개선하기 위해 고안된 것이 바로 wait()과 notify()이다. 동기화된 임계 영역의 코드를 수행하다가 작업을 더 이상 진행할 상황이 아니면, 일단 wait()을 호출하여 쓰레드가 락을 반납하고 기다리게 한다. 그러면 다른 쓰레드가 락을 얻어 해당 객체에 대한 작업을 수행할 수 있게 된다. 나중에 작업을 진행할 수 있는 상황이 되면 notify()를 호출해서, 작업을 중단했던 쓰레드가 다시 락을 얻어 작업을 진행할 수 있게 한다.

이는 마치 빵을 사려고 빵집 앞에 줄을 서있는 것과 유사한데, 자신의 차례가 되었는데도 자신이 원하는 빵이 나오지 않았으면, 다음 사람에게 순서를 양ㅇ보하고 기다리다가 자신이 원하는 빵이 나오면 통보를 받고 빵을 사가는 것이다.

차이가 있다면, 오래 기다린 쓰레드가 락을 얻는다는 보장이 없다는 것이다. wait()이 호출되면, 실행 중이던 쓰레드는 해당 객체의 대기실(waiting pool)에서 통지를 기다린다. notify()가 호출되면, 해당 객체의 대기실에 있던 모든 쓰레드 중에서 임의의 쓰레드만 통지를 받는다. notifyAll()은 기다리고 있는 모든 쓰레드에게 통보를 하지만, 그래도 lock을 얻을 수 있는 것은 하나의 쓰레드일 뿐이고 나머지 쓰레드는 통보를 받긴 했지만, lock을 얻지 못하면 다시 lock을 기다리는 신세가 된다.

wait()과 notify()는 특정 객체에 대한 것이므로 Object클래스에 정의되어 있다. wait()은 notify() 또는 notifyAll()이 호출될 때까지 기다리지만, 매개변수가 있는 wait()은 지정된 시간동안만 기다린다. 즉, 지정된 시간이 지난 후에 자동적으로 notify()가 호출되는 것과 같다.

그리고 waiting pool은 객체마다 존재하는 것이므로 notifyAll()이 호출된다고 해서 모든 객체의 waiting pool에 있는 쓰레드가 깨워지는 것이 아니다. notifyAll()이 호출된 객체의 waiting pool에 대기 중인 쓰레드만 해당된다는 것을 기억해야 한다.

> wait(), notify(), notifyAll()
> - Object에 정의되어 있다.
> - 동기화 블럭(synchronized 블럭) 내에서만 사용할 수 있다.
> - 보다 효율적인 동기화를 가능하게 한다.


## wait()과 notify() 예제 1

```java
import java.util.ArrayList;

class Customer implements Runnable {
	private Table  table;
	private String food;

	Customer(Table table, String food) {
		this.table = table;  
		this.food  = food;
	}

	public void run() {
		while (true) {
			try { Thread.sleep(10);} catch(InterruptedException e) {}
			String name = Thread.currentThread().getName();

			if(eatFood())
				System.out.println(name + " ate a " + food);
			else 
				System.out.println(name + " failed to eat. :(");
		} // while
	}

	boolean eatFood() { return table.remove(food); }
}

class Cook implements Runnable {
	private Table table;

	Cook(Table table) {	this.table = table; }

	public void run() {
		while(true) {
			int idx = (int)(Math.random() * table.dishNum());
			table.add(table.dishNames[idx]);
			try { Thread.sleep(100);} catch(InterruptedException e) {}
		} // while
	}
}

class Table {
	String[] dishNames = { "donut","donut","burger" };
	final int MAX_FOOD = 6;
	private ArrayList<String> dishes = new ArrayList<>();
	public synchronized void add(String dish) { // synchronized�� �߰�
		if(dishes.size() >= MAX_FOOD)	
			return;
		dishes.add(dish);
		System.out.println("Dishes:" + dishes.toString());
	}

	public boolean remove(String dishName) {
		synchronized(this) {	
			while(dishes.size()==0) {
				String name = Thread.currentThread().getName();
				System.out.println(name+" is waiting.");
				try { Thread.sleep(500);} catch(InterruptedException e) {}	
			}

			for(int i=0; i<dishes.size();i++)
				if(dishName.equals(dishes.get(i))) {
					dishes.remove(i);
					return true;
				}
		} // synchronized

		return false;
	}

	public int dishNum() { return dishNames.length; }
}

class Ex13_14 {
	public static void main(String[] args) throws Exception {
		Table table = new Table(); // 여러 쓰레드가 공유하는 객체 

		new Thread(new Cook(table), "COOK").start();
		new Thread(new Customer(table, "donut"),  "CUST1").start();
		new Thread(new Customer(table, "burger"), "CUST2").start();

		Thread.sleep(5000);
		System.exit(0);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Dishes:[burger]
CUST1 failed to eat. :(
CUST2 ate a burger
CUST1 is waiting.
CUST1 is waiting.
CUST1 is waiting.
CUST1 is waiting.
CUST1 is waiting.
CUST1 is waiting.
CUST1 is waiting.
CUST1 is waiting.
CUST1 is waiting.
CUST1 is waiting.
```

여러 쓰레드가 공유하는 객체인 테이블(Table)의 add()와 remove()를 동기화하였다. 더 이상 전과 같은 예외는 발생하지 않지만, 뭔가 원활히 진행되고 있는 것 같지는 않다.

손님 쓰레드가 원하는 음식이 테이블에 없으면, 'failed to eat'을 출력하고, 테이블에 음식이 하나도 없으면, 0.5초마다 음식이 추가되었는지 확인하면서 기다리도록 작성되어 있다.

그런데, 요리사 쓰레드는 왜 음식을 추가하지 않고 손님 쓰레드를 계속 기다리게 하는 것일까?

```java
synchronized(this) {
    while (dishes.size() == 0) {
        String name = Thread.currentThread().getName();
        System.out.println(name + " is waiting.");
        try { Thread.sleep(500); } catch (InterruptedException e) {}
    }
            ...
}   // synchronized의 끝
```

그 이유는 손님 쓰레드가 테이블 객체의 lock을 쥐고 기다리기 때문이다. 요리사 쓰레드가 음식을 새로 추가하려 해도 테이블 객체의 lock을 얻을 수 없어서 불가능하다. 이럴 때 사용하는 것이 바로 'wait() & notify()'이다. 손님 쓰레드가 lock을 쥐고 기다리는 게 아니라, wait()으로 lock을 풀고 기다리다가 음식이 추가되면 notify()로 통보를 받고 다시 lock을 얻어서 나머지 작업을 진행하게 할 수 있다.

이 예제에 wait()과 notify()를 적용하여 개선한 것이 다음 예제이다.


## wait()과 notify() 예제 2

```java
import java.util.ArrayList;

class Customer2 implements Runnable {
	private Table2  table;
	private String food;

	Customer2(Table2 table, String food) {
		this.table = table;  
		this.food  = food;
	}

	public void run() {
		while(true) {
			try { Thread.sleep(100);} catch(InterruptedException e) {}
			String name = Thread.currentThread().getName();
			
			table.remove(food);
			System.out.println(name + " ate a " + food);
		} // while
	}
}

class Cook2 implements Runnable {
	private Table2 table;
	
	Cook2(Table2 table) { this.table = table; }

	public void run() {
		while(true) {
			int idx = (int)(Math.random()*table.dishNum());
			table.add(table.dishNames[idx]);
			try { Thread.sleep(10);} catch(InterruptedException e) {}
		} // while
	}
}

class Table2 {
	String[] dishNames = { "donut","donut","burger" }; // donut의 확률을 높인다. 
	final int MAX_FOOD = 6;
	private ArrayList<String> dishes = new ArrayList<>();

	public synchronized void add(String dish) {
		while(dishes.size() >= MAX_FOOD) {
				String name = Thread.currentThread().getName();
				System.out.println(name+" is waiting.");
				try {
					wait(); // COOK쓰레드를 기다리게 한다. 
					Thread.sleep(500);
				} catch(InterruptedException e) {}	
		}
		dishes.add(dish);
		notify();  // 기다리고 있는 CUST를 깨우기 위함. 
		System.out.println("Dishes:" + dishes.toString());
	}

	public void remove(String dishName) {
		synchronized(this) {	
			String name = Thread.currentThread().getName();

			while(dishes.size()==0) {
					System.out.println(name+" is waiting.");
					try {
						wait(); // CUST쓰레드를 기다리게 한다. 
						Thread.sleep(500);
					} catch(InterruptedException e) {}	
			}

			while(true) {
				for(int i=0; i<dishes.size();i++) {
					if(dishName.equals(dishes.get(i))) {
						dishes.remove(i);
						notify(); // 잠자고 있는 COOK을 깨우기 위함 
						return;
					}
				} // for문의 끝 

				try {
					System.out.println(name+" is waiting.");
					wait(); // 원하는 음식이 없는 CUST쓰레드를 기다리게 한다. 
					Thread.sleep(500);
				} catch(InterruptedException e) {}	
			} // while(true)
		} // synchronized
	}
	public int dishNum() { return dishNames.length; }
}

class Ex13_15 {
	public static void main(String[] args) throws Exception {
		Table2 table = new Table2();

		new Thread(new Cook2(table), "COOK").start();
		new Thread(new Customer2(table, "donut"),  "CUST1").start();
		new Thread(new Customer2(table, "burger"), "CUST2").start();
		Thread.sleep(2000);
		System.exit(0);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Dishes:[donut]
Dishes:[donut, donut]
Dishes:[donut, donut, donut]
Dishes:[donut, donut, donut, donut]
Dishes:[donut, donut, donut, donut, donut]
Dishes:[donut, donut, donut, donut, donut, donut]
COOK is waiting.
CUST2 is waiting.
CUST1 ate a donut
Dishes:[donut, donut, donut, donut, donut, donut]
CUST2 is waiting.
COOK is waiting.
CUST1 ate a donut
CUST2 is waiting.
CUST1 ate a donut
```

이전 예제에 wait()과 notify()를 추가하였다. 그리고 테이블에 음식이 없을 때뿐만 아니라, 원하는 음식이 없을 때도 손님이 기다리도록 바꾸었다.

실행 결과를 보니 이제 뭔가 좀 잘 돌아가는 것 같다. 그런데, 여기에도 한 가지 문제가 있다. 테이블 객체의 waiting pool에 요리사 쓰레드와 손님 쓰레드가 같이 기다린다는 것이다. 그래서 notify()가 호출되었을 때, 요리사 쓰레드와 손님 쓰레드 중에서 누가 통지를 받을지 알 수 없다.

만일 테이블의 음식이 줄어들어서 notify()가 호출되었다면, 요리사 쓰레드가 통제를 받아야 한다. 그러나 notify()는 그저 waiting pool에서 대기 중인 쓰레드 중에서 하나를 임의로 선택해서 통지할 뿐, 요리사 쓰레드를 선택해서 통지할 수 없다. 운 좋게 요리사 쓰레드가 통지를 받으면 다행인데, 손님 쓰레드가 통지를 받으면 lock을 얻어도 여전히 자신이 원하는 음식이 없어서 다시 waiting pool에 들어가게 된다.