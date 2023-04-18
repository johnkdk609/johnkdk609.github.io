---
layout: post
title: 여러 종류의 객체를 배열로 다루기
description: Java의 정석 기초편 ch. 7 객체지향 프로그래밍 2
categories: Java
date: 2023-04-18 12:33:00 +09:00
---
조상 타입의 참조변수로 자손 타입의 객체를 참조하는 것이 가능하므로, Product 클래스가 Tv, Computer, Audio 클래스의 조상일 때, 다음과 같이 할 수 있는 것을 이미 배웠다.

```java
Product p1 = new Tv();
Product p2 = new Computer();
Product p3 = new Audio();
```

위의 코드를 Product 타입의 참조변수 배열로 처리하면 아래와 같다.

```java
Product p[] = new Product[3];
p[0] = new Tv();
p[1] = new Computer();
p[2] = new Audio();
```

이처럼 조상 타입의 참조변수 배열을 사용하면, 공통의 조상을 가진 서로 다른 종류의 객체를 배열로 묶어서 다룰 수 있다. 또는 묶어서 다루고 싶은 객체들의 상속 관계를 따져서 가장 가까운 공통조상 클래스 타입의 참조변수 배열을 생성해서 객체들을 저장하면 된다. 

이러한 특징을 이용해서 이전에 보았던 예제 7-8의 Buyer 클래스에 구입한 제품을 저장하기 위한 Product 배열을 추가해보면 다음과 같다.

```java
class Buyer {
  int money = 1000;
  int bonusPoint = 0;
  Product[] cart = new Product[10];   // 구입한 제품을 저장하기 위한 배열(카트)
  int i = 0;                          // Product 배열 cart에 사용될 index
  
  void buy(Product p) {
    if (money < p.price) {
      System.out.println("잔액이 부족하여 물건을 살 수 없습니다.");
      return;
    }
    
    money -= p.price;                 // 가진 돈에서 제품 가격을 뺀다.
    bonusPoint += p.bonusPoint;       // 제품의 보너스 포인트를 더한다.
    cart[i++] = p;                    // 제품을 Product[] cart에 저장한다.
    System.out.println(p + "을/를 구입하셨습니다.");
  }
}
```

구입한 제품을 담기 위해 Buyer 클래스에 Product 배열인 cart를 추가해주었다. 그리고 buy 메서드에 'cart[i++] = p;' 문장을 추가함으로써 물건을 구입하면, 배열 cart에 저장되도록 했다. 이렇게 함으로써, 모든 제품 클래스의 조상인 Product 클래스 타입의 배열을 사용함으로써 구입한 제품을 하나의 배열로 간단하게 다룰 수 있게 된다.


### 에제 코드 구현

```java
class Product2 {
	int price;        // 제품의 가격 
	int bonusPoint;   // 제품 구매 시 제공하는 보너스 점수 

	Product2(int price) {
		this.price = price;
		bonusPoint = (int)(price/10.0);
	}

	Product2() {}		// 기본 생성자 
}

class Tv2 extends Product2 {		// 조상 클래스의 생성자. Product2(int price)를 호출한다. 
	Tv2() {  super(100);	 }

	public String toString() { return "Tv"; }
}

class Computer2 extends Product2 {
	Computer2() { super(200); }
	public String toString() { return "Computer"; }
}

class Audio2 extends Product2 {
	Audio2() { super(50); }
	public String toString() { return "Audio"; }
}

class Buyer2 {        // 고객. 물건을 사는 사람 
	int money = 1000;   // 소유 금액 
	int bonusPoint = 0; // 보너스 점수 
	Product2[] cart = new Product2[10];   // 구입한 제품을 저장하기 위한 배열 
	int i =0;           // Product 배열에 사용될 카운터 

	void buy(Product2 p) {
		if(money < p.price) {
			System.out.println("잔액이 부족하여 물건을 살 수 없습니다.");
			return;
		}

		money -= p.price;				// 가진 돈에서 구입한 제품의 가격을 뺀다. 
		bonusPoint += p.bonusPoint;		// 제품의 보너스 점수를 추가한다. 
		cart[i++] = p;					// 제품을 Product[] cart에 저장한다. 
		System.out.println(p + "을/를 구입하셨습니다.");
	}

	void summary() {          // 구매한 물품에 대한 정보를 요약해서 보여 준다.
		int sum = 0;            // 구입한 물품의 가격 합계 
		String itemList ="";    // 구입한 물품 목록 

		// 반복문을 이용해서 구입한 물품의 총 가격과 목록을 만든다. 
		for(int i=0; i<cart.length;i++) {
			if(cart[i]==null) break;
			sum += cart[i].price;
			itemList += (i==0) ? "" + cart[i] : ", " + cart[i];
		}
		System.out.println("구입하신 물품의 총 금액은 " + sum + "만원입니다.");
		System.out.println("구입하신 제품은 " + itemList + "입니다.");
	}
}

class Ex7_9 {
	public static void main(String args[]) {
		Buyer2 b = new Buyer2();

		b.buy(new Tv2());
		b.buy(new Computer2());
		b.buy(new Audio2());
		b.summary();
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Tv을/를 구입하셨습니다.
Computer을/를 구입하셨습니다.
Audio을/를 구입하셨습니다.
구입하신 물품의 총 금액은 350만원입니다.
구입하신 제품은 Tv, Computer, Audio입니다.
```

위 예제에서 Product2 배열로 구입한 제품들을 저장할 수 있도록 했지만, 배열의 크기를 10으로 했기 때문에 11개 이상의 제품을 구입할 수 없는 것이 문제다. 그렇다고 해서 배열의 크기를 무조건 크게 설정할 수는 없는 일이다. 

이런 경우, Vector 클래스를 사용하면 된다. Vector 클래스는 내부적으로 Object 타입의 배열을 가지고 있어서, 이 배열에 객체를 추가하거나 제거할 수 있게 작성되어 있다.

그리고 배열의 크기를 알아서 관리해주기 때문에 저장할 인스턴스의 개수에 신경쓰지 않아도 된다.

```java
public class Vector extends AbstractList implements List, Cloneable, java.io.Serializable {
  protected Object elementData[];
  // ...
}
```

Vector 클래스는 이름 때문에 클래스의 기능을 오해할 수 있는데, 단지 동적으로 크기가 관리되는 객체배열일 뿐이다.
