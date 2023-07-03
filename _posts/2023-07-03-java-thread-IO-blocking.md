---
layout: post
title: 쓰레드의 I/O블락킹(blocking)
description: Java의 정석 기초편 ch. 13 쓰레드
categories: Java
date: 2023-07-03 16:01:00 +0900
---
두 쓰레드가 서로 다른 자원을 사용하는 작업의 경우에는 싱글쓰레드 프로세스보다 멀티쓰레드 프로세스가 더 효율적이다. 예를 들면 사용자로부터 데이터를 입력받는 작업, 네트워크로 파일을 주고받는 방법, 프린터로 파일을 출력하는 작업과 같이 외부기기와의 입출력을 필요로 하는 경우가 이에 해당한다.

<img width="550" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/9ddb8123-b173-49ad-b139-0934bdfb531c">

<img width="550" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7711cc43-1584-4282-aef0-abed37d15f40">

위 그림들은 싱글쓰레드 프로세스(위)와 멀티쓰레드 프로세스(아래)의 비교를 나타낸 것이다.

만일 사용자로부터 입력받는 작업(A)와 화면에 출력하는 작업(B)을 하나의 쓰레드로 처리한다면 위 그림 중 위에 것처럼 사용자가 입력을 마칠 때까지 아무 일도 하지 못하고 기다리기만 해야 한다.

그러나 두 개의 쓰레드로 처리한다면 사용자의 입력을 기다리는 동안 다른 쓰레드가 작업을 처리할 수 있기 때문에 보다 효율적인 CPU의 사용이 가능하다.

작업 A와 B가 모두 종료되는 시간 t2와 t2'를 비교하면 t2 > t2'로 멀티 쓰레드 프로세스의 경우가 작업을 더 빨리 마치는 것을 알 수 있다.

(쓰레드가 입출력(I/O)처리를 위해 기다리는 것을 I/O블락킹이라고 한다.)


## 쓰레드의 I/O블락킹(blocking) 예제 1

```java
import javax.swing.JOptionPane;

class Ex13_4 {
	public static void main(String[] args) throws Exception {
		String input = JOptionPane.showInputDialog("아무 값이나 입력하세요."); 
		System.out.println("입력하신 값은 " + input + "입니다.");

		for(int i=10; i > 0; i--) {
			System.out.println(i);
			try {
				Thread.sleep(1000);  // 1초간 시간을 지연한다. 
			} catch(Exception e ) {}
		}
	}
}
```

위 코드를 실행시키면 다음과 같은 창이 뜬다.

<img width="320" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/761427a7-ac02-41cc-b493-cfebce19a494">

여기에 'abcd'를 입력해보겠다. 그러면 다음과 같이 출력된다.

```
입력하신 값은 abcd입니다.
10
9
8
7
6
5
4
3
2
1
```

이 예제는 하나의 쓰레드로 사용자의 입력을 받는 작업과 화면의 숫자를 출력하는 작업을 처리하기 때문에, 사용자가 입력을 마치기 전까지는 화면에 숫자가 출력되지 않다가 사용자가 입력을 마치고 나서야 화면에 숫자가 출력된다.


## 쓰레드의 I/O블락킹(blocking) 예제 2

```java
import javax.swing.JOptionPane;

class Ex13_5 {
	public static void main(String[] args) throws Exception  {
		ThreadEx5_1 th1 = new ThreadEx5_1();
		th1.start();

		String input = JOptionPane.showInputDialog("아무 값이나 입력하세요."); 
		System.out.println("입력하신 값은 " + input + "입니다.");
	}
}

class ThreadEx5_1 extends Thread {
	public void run() {
		for(int i=10; i > 0; i--) {
			System.out.println(i);
			try {
				sleep(1000);
			} catch(Exception e ) {}
		}
	} // run()
}
```

위 코드를 실행시키면 10, 9, 8, ... 이렇게 출력되다가 중간에 '아무 값이나 입력하세요'라는 창이 뜬다. 거기에 'abcd'를 입력하면 입력한 값이 출력되고 나머지 숫자들도 출력된다.

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
2
1
```

이전 예제와는 달리 사용자로부터 입력받는 부분과 화면에 숫자를 출력하는 부분을 두 개의 쓰레드로 나누어서 처리했기 때문에 사용자가 입력을 마치지 않았어도 화면에 숫자가 출력되는 것을 알 수 있다.