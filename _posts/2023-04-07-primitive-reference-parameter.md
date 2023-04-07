---
layout: post
title: 기본형 매개변수와 참조형 매개변수
categories: Java
description: Java의 정석 기초편 ch. 6 객체지향 프로그래밍 1
date: 2023-04-07 21:47:00 +09:00
---
자바에서는 메서드를 호출할 때 매개변수로 지정한 값을 메서드의 매개변수에 복사해서 넘겨준다. 매개변수의 타입이 기본형(primitive type)일 때는 기본형 값이 복사되겠지만, 참조형(reference type)이면 인스턴스의 주소가 복사된다.

메서드의 매개변수를 기본형으로 선언하면 단순히 저장된 값만 얻지만, **참조형으로 선언하면 값이 저장된 곳의 주소를 알 수 있기 때문에 값을 읽어오는 것은 물론 값을 변경하는 것도 가능**하다.

> **기본형 매개변수** - 변수의 값을 읽기만 할 수 있다. (read only)\
> **참조형 매개변수** - 변수의 값을 읽고 변경할 수 있다. (read & write)


### 기본형 매개변수 예제 코드

```java
class Data { int x; }

class Ex6_6 {
	public static void main(String[] args) {
		Data d = new Data();
		d.x = 10;
		System.out.println("main() : x = " + d.x);

		change(d.x);
		System.out.println("After change(d.x)");
		System.out.println("main() : x = " + d.x);
	}

	static void change(int x) {  // 기본형 매개변수
		x = 1000;
		System.out.println("change() : x = " + x);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
main() : x = 10
change() : x = 1000
After change(d.x)
main() : x = 10   // 값 변경 1000으로 안 된 것 알 수 있음
```

위 코드를 분석하면 다음과 같다.

1. change 메서드가 호출되면서 'd.x'가 change 메서드의 매개변수 x에 복사됨
2. change 메서드에서 x의 값을 1000으로 변경
3. change 메서드가 종료되면서 매개변수 x는 스택에서 제거됨

'**d.x'의 값이 변경된 것이 아니라, change 메서드의 매개변수 x의 값이 변경된 것**이다. 즉, 원본이 아닌 복사본이 변경된 것이라 원본에는 아무런 영향을 미치지 못한다. 이처럼 **기본형 매개변수는 변수에 저장된 값을 읽을 수만 있을 뿐 변경할 수는 없다**.


### 참조형 매개변수 예제 코드

```java
class Data2 { int x; }

class Ex6_7 {
	public static void main(String[] args) {
		Data2 d = new Data2();
		d.x = 10;
		System.out.println("main() : x = " + d.x);

		change(d);
		System.out.println("After change(d)");
		System.out.println("main() : x = " + d.x);
	}

	static void change(Data2 d) { // 참조형 매개변수 
		d.x = 1000;
		System.out.println("change() : x = " + d.x);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
main() : x = 10
change() : x = 1000
After change(d)
main() : x = 1000   // 값이 1000으로 변경되었음
```

이전 예제와 달리 change 메서드를 호출한 후에 d.x의 값이 변경되었다. **change 메서드의 매개변수가 참조형이라서 값이 아니라 '값이 저장된 주소'를 change 메서드에게 넘겨주었기 때문에 값을 읽어오는 것뿐만 아니라 변경하는 것도 가능**하다.

1. change 메서드가 호출되면서 참조변수 d의 값(주소)이 매개변수 d에 복사됨.
2. change 메서드에서 매개변수 d로 x의 값을 1000으로 변경
3. change 메서드가 종료되면서 매개변수 d는 스택에서 제거됨

앞서 본 예제와 달리, change 메서드의 매개변수를 참조형으로 선언했기 때문에, x의 값이 아닌 변수 d의 주소가 매개변수 d에 복사되었다. 이제 main 메서드의 참조변수 d와 change 메서드의 참조변수 d는 같은 객체를 가리키게 된다. 그래서 매개변수 d로 x의 값을 일는 것과 변경하는 것이 모두 가능한 것이다. 이 두 예제를 잘 비교해서 차이를 완전히 이해해야 한다. 


### 참조형 반환타입

한편, 매개변수뿐만 아니라 반환타입도 참조형이 될 수 있다. 반환타입이 참조형이라는 것은 반환하는 값의 타입이 참조형이라는 얘기인데, 모든 참조형 타입의 값은 '객체의 주소'이므로 그저 정수값이 반환되는 것일 뿐 특별한 것이 없다.

```java
class Data3 { int x; }

class Ex6_8 {
	public static void main(String[] args) {
		Data3 d = new Data3();
		d.x = 10;

		Data3 d2 = copy(d); 
		System.out.println("d.x ="+d.x);
		System.out.println("d2.x="+d2.x);
	}

	static Data3 copy(Data3 d) {
		Data3 tmp = new Data3();    // 새로운 객체 tmp를 생성한다.

		tmp.x = d.x;  // d.x의 값을 tmp.x에 복사한다.

		return tmp;   // 복사한 객체의 주소를 반환한다.
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
d.x =10
d2.x=10
```

copy 메서드는 새로운 객체를 생성한 다음에, 매개변수로 넘겨받은 객체에 저장된 값을 복사해서 반환한다. 반환하는 값이 Data 객체의 주소이므로 반환 타입이 'Data'인 것이다.
