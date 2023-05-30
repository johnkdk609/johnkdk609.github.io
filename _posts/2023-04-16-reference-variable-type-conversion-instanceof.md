---
layout: post
title: 참조변수의 형변환과 instanceof 연산자
description: Java의 정석 기초편 ch. 7 객체지향 프로그래밍 2
categories: Java
date: 2023-04-16 16:16:00 +09:00
---
기본형 변수처럼 참조변수도 형변환이 가능하다. 단, 서로 상속 관계에 있는 클래스 사이에서만 가능하기 때문에 자손 타입의 참조변수를 조상 타입의 참조변수로, 조상 타입의 참조변수를 자손 타입의 참조변수로의 형변환만 가능하다. 

(바로 윗 조상이나 자손이 아닌, 조상의 조상으로도 형변환이 가능하다. 따라서 모든 참조변수는 모든 클래스의 조상인 Object 클래스 타입으로 형변환이 가능하다.)

```java
class Car { }
class FireEngine extends Car { }
class Ambulance extends Car { }
```

위와 같이 Car 클래스가 있고 이를 상속받는 FireEngine, Ambulance 클래스가 있을 때, FireEngine 타입의 참조변수 f는 조상 타입인 Car로 형변환 가능하다. 반대로 Car 타입의 참조변수를 자손 타입인 FireEngine으로 형변환하는 것도 가능하다. 그러나 FireEngine과 Ambulance는 상속관계가 아니므로 형변환이 불가능하다. 

```java
FireEngine f = new FireEngine();

Car c = (Car)f;                   // OK. 조상인 Car 타입으로 형변환(생략 가능)
FireEngine f2 = (FireEngine)c;    // OK. 자손인 FireEngine 타입으로 형변환(생략 불가)
Ambulance a = (Ambulance)f;       // ERROR. 상속관계가 아닌 클래스 간의 형변환 불가
```

기본형의 형변환과 달리 참조형의 형변환은 변수에 저장된 값(주소값)이 변환되는 것이 아니다.

```java
Car c = f;            // f의 값(객체의 주소)을 c에 저장
                      // 타입을 일치시키기 위해 형변환 필요(생략 가능)
f = (FireEngine)c;    // 조상 타입을 자손 타입으로 형변환하는 경우 생략 불가
```

참조변수의 형변환은 그저 리모컨(참조변수)을 다른 종류의 것으로 바꾸는 것뿐이다. 리모컨의 타입을 바꾸는 이유는 사용할 수 있는 멤버 개수를 조절하기 위한 것이다.

위와 같이 조상 타입으로의 형변환을 생략할 수 있는 이유는 **조상 타입으로 형변환하면 다룰 수 있는 멤버의 개수가 줄어들기 때문에 항상 안전하기 때문**이다. 반대로 실제 객체가 가진 기능보다 리모컨의 버튼이 더 많으면 안 된다.

> 서로 상속관계에 있는 타입 간의 형변환은 양방향으로 자유롭게 수행될 수 있으나, **참조변수가 가리키는 인스턴스의 자손 타입으로 형변환은 허용되지 않는다.**
> 그래서 참조변수가 가리키는 인스턴스의 타입이 무엇인지 먼저 확인하는 것이 중요하다.


### 참조변수의 형변환 예제

```java
class Ex7_7 {
	public static void main(String args[]) {
		Car car = null;
		FireEngine fe = new FireEngine();
		FireEngine fe2 = null;

		fe.water();
		car = fe;    			// car = (Car)fe;에서 형변환이 생략됨. 
//		car.water();			// 컴파일 에러. Car 타입의 참조변수로는 water()를 호출할 수 없다. 
		fe2 = (FireEngine)car;		// 자손 타입 <- 조상 타입. 형변환 생략 불가 
		fe2.water();
	}
}

class Car {
	String color;
	int door;

	void drive() { 	// 운전하는 기능 
		System.out.println("drive, Brrrr~");
	}

	void stop() {  	// 멈추는 기능 	
		System.out.println("stop!!!");	
	}
}

class FireEngine extends Car {	// 소방차 
	void water() {	// 물을 뿌리는 기능 
		System.out.println("water!!!");
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
water!!!
water!!!
```

이 예제에서 중요한 부분은 다음 두 줄이다.

```java
FireEngine fe = new FireEngine();   // FireEngine 객체를 생성
car = fe;   // fe의 값을 car에 저장. (형변환 생략됨)
```

참조변수 fe의 값을 car에 저장해서 car로도 FireEngine 인스턴스를 다룰 수 있게 되었다. 다만 참조변수 fe와 달리 car로는 FireEngine 인스턴스의 멤버 중 4개만 사용 가능하다.


### instanceof 연산자

참조변수가 참조하고 있는 인스턴스의 실제 타입을 알아보기 위해 instanceof 연산자를 사용한다.

주로 조건문에 사용되며, instanceof의 왼쪽에는 참조변수를 오른쪽에는 타입(클래스명)이 피연산자로 위치한다. 그리고 연산의 결과로 boolean 값인 true와 false 중의 하나를 반환한다.

instanceof를 이용한 연산 결과로 true를 얻었다는 것은 참조변수가 검사한 타입으로 형변환이 가능하다는 것을 뜻한다.

(값이 null인 참조변수에 대해 instanceof 연산을 수행하면 false를 결과로 얻는다.)

```java
void doWork(Car c) {
	if (c instanceof FireEngine) {			// 1. 형변환이 가능한지 확인
		FireEngine fe = (FireEngine)c;		// 2. 형변환
		fe.water();
		// ...
}
```

위의 코드는 Car 타입의 참조변수 c를 매개변수로 하는 메서드이다. 이 메서드가 호출될 때, 매개변수로 Car 클래스 또는 그 자손 클래스의 인스턴스를 넘겨받겠지만 메서드 내에서는 정확히 어떤 인스턴스인지 알 길이 없다. 그래서 instanceof 연산자로 참조변수 c가 가리키고 있는 인스턴스의 타입을 체크하고, 적절히 형변환한 다음에 작업을 해야 한다. 

조상 타입의 참조변수로 자손 타입의 인스턴스를 참조할 수 있기 때문에, 참조변수의 타입과 인스턴스의 타입이 항상 일치하지는 않는다는 것을 배웠다. 조상 타입의 참조변수로는 실제 인스턴스의 멤버들을 모두 사용할 수 없기 때문에, 실제 인스턴스와 같은 타입의 참조변수로 형변환을 해야만 인스턴스의 모든 멤버들을 사용할 수 있다.

> 어떤 타입에 대한 instanceof 연산의 결과가 true라는 것은 검사한 타입으로 형변환이 가능하다는 것을 뜻한다.
