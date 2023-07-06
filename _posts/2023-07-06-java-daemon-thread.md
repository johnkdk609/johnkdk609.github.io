---
layout: post
title: 데몬 쓰레드(deamon thread)
description: Java의 정석 기초편 ch. 13 쓰레드
categories: Java
date: 2023-07-06 11:45:00 +0900
---
데몬 쓰레드(daemon thread)는 다른 일반 쓰레드(데몬 쓰레드가 아닌 쓰레드)의 작업을 돕는 보조적인 역할을 수행하는 쓰레드이다. 일반 쓰레드가 모두 종료되면 데몬 쓰레드는 강제적으로 자동 종료되는데, 그 이유는 데몬 쓰레드는 일반 쓰레드의 보조역할을 수행하므로 일반 쓰레드가 모두 종료되고 나면 데몬 쓰레드의 존재가 의미가 없기 때문이다.

이 점을 제외하고는 데몬 쓰레드와 일반 쓰레드는 다르지 않다. 데몬 쓰레드의 예로는 가비지 컬렉터, 워드프로세서의 자동저장, 화면자동갱신 등이 있다.

데몬 쓰레드는 무한루프와 조건문을 이용해서 실행 후 대기하고 있다가 특정 조건이 만족되면 작업을 수행하고 다시 대기하도록 작성한다.

```java
public void run() {
    while (true) {
        try {
            Thread.sleep(3 * 1000);     // 3초마다
        } catch (InterruptedException e) {}

        // autoSave의 값이 true이면 autoSave()를 호출한다.
        if (autoSave) autoSave();
    }
}
```

데몬 쓰레드는 일반 쓰레드의 작성방법과 실행방법이 같으며 다만 쓰레드를 생성한 다음 실행하기 전에 setDaemon(true)를 호출하기만 하면 된다. 그리고 데몬 쓰레드가 생성한 쓰레드는 자동적으로 데몬 쓰레드가 된다.

> boolean isDaemon() - 쓰레드가 데몬 쓰레드인지 확인한다. 데몬 쓰레드이면 true를 반환한다.\
> void setDaemon(boolean on) - 쓰레드를 데몬 쓰레드로 또는 사용자 쓰레드로 변경한다. 매개변수 on의 값을 true로 지정하면 데몬 쓰레드가 된다.


## 데몬 쓰레드(daemon thread) 예제

```java
class Ex13_7 implements Runnable  {
	static boolean autoSave = false;

	public static void main(String[] args) {
		Thread t = new Thread(new Ex13_7());
		t.setDaemon(true);		// 이 부분이 없으면 종료되지 않는다. 
		t.start();

		for(int i=1; i <= 10; i++) {
			try{
				Thread.sleep(1000);
			} catch(InterruptedException e) {}
			System.out.println(i);

			if(i==5) autoSave = true;
		}

		System.out.println("프로그램을 종료합니다.");
	}

	public void run() {
		while(true) {
			try { 
				Thread.sleep(3 * 1000); // 3초마다 
			} catch(InterruptedException e) {}

			// autoSave의 값이 true이면 autoSave()를 호출한다. 
			if (autoSave) autoSave();
		}
	}

	public void autoSave() {
		System.out.println("작업파일이 자동저장되었습니다.");
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```1
2
3
4
5
작업파일이 자동저장되었습니다.
6
7
8
작업파일이 자동저장되었습니다.
9
10
프로그램을 종료합니다.
```

3초마다 변수 autoSave의 값을 확인해서 그 값이 true이면, autoSave()를 호출하는 일을 무한히 반복하도록 쓰레드를 작성하였다. 만일 이 쓰레드를 데몬 쓰레드로 설정하지 않았다면, 이 프로그램은 강제종료하지 않는 한 영원히 종료되지 않을 것이다.

```java
Thread t = new Thread(new Ex13_7());

t.setDaemon(true);      // 데몬 쓰레드로 지정. 이 부분이 없으면 종료되지 않는다.
t.start();
```

setDaemon메서드는 반드시 start()를 호출하기 전에 실행되어야 한다. 그렇지 않으면 IllegalThreadStateException이 발생한다.