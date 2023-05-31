---
layout: post
title: 매개변수의 다형성
description: Java의 정석 기초편 ch. 7 객체지향 프로그래밍 2
categories: Java
date: 2023-04-17 16:51:00 +09:00
---
참조변수의 다형적인 특징은 메서드의 매개변수에도 적용된다.

```java
class Product {
  int price;          // 제품의 가격
  int bonusPoint;     // 제품 구매시 제공하는 보너스 점수
}

class Tv extends Product {}
class Computer extends Product {}

class Buyer {         // 고객, 물건을 사는 사람
  int money = 1000;   // 소유 금액
  int bonusPoint = 0; // 보너스 점수
}
```

위와 같이 Product, Tv, Computer, Buyer 클래스가 정의되어 있을 때

Product 클래스는 Tv와 Computer 클래스의 조상이며, Buyer 클래스는 제품(Product)를 구입하는 사람을 클래스로 표현한 것이다.

Buyer 클래스에 물건을 구입하는 기능의 메서드를 추가해보겠다. 구입할 대상이 필요하므로 매개변수로 구입할 제품을 넘겨받아야 한다. Tv를 살 수 있도록 매개변수를 Tv 타입으로 하였다.

```java
void buy(Tv t) {
  // Buyer이 가진 돈(money)에서 제품의 가격(t.price)만큼 뺀다.
  money = money - t.price;
  
  // Buyer의 보너스 점수(bonusPoint)에 제품의 보너스 점수(t.bonusPoint)를 더한다.
  bonusPoint = bonusPoint + t.bonusPoint;
}
```

buy(Tv t)는 제품을 구입하면 제품을 구입한 사람이 가진 돈에서 제품의 가격을 빼고, 보너스 점수는 추가하는 작업을 하도록 작성되었다. 그런데 buy(Tv t)로는 Tv밖에 살 수 없기 때문에 아래와 같이 다른 제품들도 구입할 수 있는 메서드가 추가로 필요하다.

```java
void buy(Computer c) {
  money = money - c.price;
  bonusPoint = bonusPoint + c.bonusPoint;
}
```

이렇게 되면, 제품의 종류가 늘어날 때마다 Buyer 클래스에는 새로운 buy 메서드를 추가해주어야 할 것이다.

그러나 메서드의 매개변수에 다형성을 적용하면 아래와 같이 하나의 메서드로 간단히 처리할 수 있다.

```java
void buy(Product p) {
  money -= p.price;
  bonusPoint += p.bonusPoint;
}
```

매개변수가 Product 타입의 참조변수라는 것은, 메서드의 매개변수로 Product 클래스의 자손 타입의 참조변수면 어느 것이나 매개변수로 받아들일 수 있다는 뜻이다.

그리고 Product 클래스에 price와 bonusPoint가 선언되어 있기 때문에 참조변수 p로 인스턴스의 price와 bonusPoint를 사용할 수 있기에 이와 같이 할 수 있다.

앞으로 다른 제품 클래스를 추가할 때 Product클래스를 상속받기만 하면, buy(Product p) 메서드의 매개변수로 받아들여질 수 있다.

```java
Buyer b = new Buyer();
Tv t = new Tv();
Computer c = new Computer;
b.buy(t);
b.buy(c);
```

(Tv t = new Tv(); b.buy(t); 를 한 문장으로 줄이면, b.buy(new Tv(t)); 가 된다.)

Tv 클래스와 Computer 클래스는 Product 클래스의 자손이므로 위의 코드와 같이 buy(Product p) 메서드의 매개변수로 Tv 인스턴스와 Computer 인스턴스를 제공하는 것이 가능하다.


### 매개변수의 다형성 예제 코드

```java
class Product {
	int price;		// 제품의 가격 
	int bonusPoint;		// 제품 구매 시 제공하는 보너스 점수 

	Product(int price) {
		this.price = price;
		bonusPoint = (int)(price/10.0);		// 보너스 점수는 제품 가격의 10% 
	}
}

class Tv1 extends Product {
	Tv1() {
		// 조상 클래스의 생성자 Product(int price)를 호출한다. 
		super(100);	// Tv의 가격을 100만원으로 한다. 
	}

	// Object클래스의 toString()을 오버라이딩 한다. 
	public String toString() { return "Tv"; }
}

class Computer extends Product {
	Computer() { super(200); }

	public String toString() { return "Computer"; }
}

class Buyer {			// 고객, 물건을 사는 사람 
	int money = 1000;	// 소유 금액 
	int bonusPoint = 0;	// 보너스 점수 

	void buy(Product p) {
		if(money < p.price) {
			System.out.println("잔액이 부족하여 물건을 살 수 없습니다.");
			return;
		}

		money -= p.price;            // 가진 돈에서 구입한 제품의 가격을 뺀다. 
		bonusPoint += p.bonusPoint;  // 제품의 보너스 점수를 추가한다. 
		System.out.println(p + "을/를 구입하셨습니다.");
	}
}

class Ex7_8 {
	public static void main(String args[]) {
		Buyer b = new Buyer();

		b.buy(new Tv1());
		b.buy(new Computer());

		System.out.println("현재 남은 돈은 " + b.money + "만원입니다.");
		System.out.println("현재 보너스 점수는 " + b.bonusPoint + "점입니다.");
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Tv을/를 구입하셨습니다.
Computer을/를 구입하셨습니다.
현재 남은 돈은 700만원입니다.
현재 보너스 점수는 30점입니다.
```

('참조변수+문자열'은 '참조변수.toString()+문자열'로 처리된다.)
