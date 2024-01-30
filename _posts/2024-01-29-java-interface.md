---
layout: post
title: 인터페이스 (Interface)
categories: Java
date: 2024-01-29 21:44:00 +0900
---
인터페이스(interface)란 무엇일까?

인터페이스는 서로 다른 장치들을 연결시켜 주는 규격이다. '접점'인 것이다.

가령 HDMI 단자에 맞는 케이블을 꽂아 모니터, 프로젝터 등을 사용할 수 있다.

<br>

자바에서 인터페이스는 <b>완벽히 추상화된 설계도</b>이다. 클래스와 유사하게 작성이 가능하지만, 모든 메서드가 추상 메서드인 것이다. 즉, 구현부가 없다. <u>어떠한 메서드를 구현하겠다는 약속</u>이다.

(물론, 구현부가 존재하는 default method, static method라는 것들이 존재하기는 한다.)

```java
public interface 인터페이스이름 {
    public static final 타입 상수이름1 = 10;
    타입 상수이름2 = 20;    // public static final은 생략 가능하다.

    public abstract void 메서드이름1(타입 매개변수...);
    void 메서드이름2(타입 매개변수...);     // public abstract은 생략 가능하다.
}
```

인터페이스에 있는 모든 변수는 ```public static final```이다. 기입할 때 이 부분을 생략할 수 있다.

<br>

## 인터페이스 구현

인터페이스는 그 자체로 인스턴스를 생성할 수 없다. (구현부가 없기 때문이다.)

```java
interface MyInterface {}

public class Main {
    MyInterface m = new MyInterface();  // 오류 발생
}
```

인터페이스 내에 있는 메서드를 구현할 클래스가 필요하다. 이때 implements 키워드를 사용하여 구현 클래스를 작성한다. (상속할 때 사용하는 extends 키워드와 혼동하면 안 된다.)

```java
class Circle implements Shape {}
```

그리고 여러 개의 interface를 implements 하는 것이 가능하다. (다중 구현 가능)

<br>

<b>인터페이스를 구현하는 클래스는 추상 메서드를 반드시 오버라이딩(재정의) 해야 한다.</b> 만약 <b>구현하지 않을 경우 abstract 클래스로 표시해야</b> 한다.

다음 예시 코드를 보자.

```java
interface Chef {
    void cook();
}
```

```java
class KFoodChef implements Chef {

    @Override
    public void cook() {
        System.out.println("한식을 요리한다.");
    }
}
```

```java
abstract class JFoodChef implements Chef {

}
```

위 코드에서 JFoodChef는 Chef 인터페이스를 implements 했는데, cook() 메서드를 재정의하지 않았기 때문에 abstract 클래스인 것이다.

<br>

<u>만약 해당 객체가 그 인터페이스를 구현했다면, 인터페이스를 참조형 타입으로 활용할 수 있다.</u>

```java
public static void main(String[] args) {
    Chef chef = new KFoodChef();
}
```

즉, <b>다형성이 적용된 것이다.</b>

<br>

## 인터페이스 상속

인터페이스 간에 상속이 가능하다. 이때는 extends 키워드를 이용한다.

특이한 점은, 다중 상속이 가능하다는 것이다. (클래스는 단일 상속만 가능하다는 점에서 차이가 있다.)

다음 코드를 보자.

```java
interface Movable {
    public abstract void move();
}
```

```java
interface Cookable {
    public abstract void cook();
}
```

```java
interface Chef extends Movable, Cookable {

}
```

```java
class Robot implements Chef {

    @Override
    public void move() { /* 생략 */ }

    @Override
    public void cook() { /* 생략 */ }
}
```

Chef 인터페이스는 Movable, Cookable 인터페이스를 동시에 상속 받았다. 그리고 그런 Chef 인터페이스를 implements 한 Robot 클래스는 move()와 cook()를 모두 재정의해야 한다. (재정의하지 않으면 abstract class가 된다.)

<br>

## default method

인터페이스에 구현부가 있는 메서드를 작성할 수 있기는 하다. 그러려면 메서드 앞에 default라는 키워드를 작성해야 한다.

public 접근 제한자를 사용해야 하며 생략이 가능하다.

```java
interface Chef extends Movable, Cookable {
    public void info();
}
```

⇩

```java
interface Chef extends Movable, Cookable {
    public default void info() { /* 구현 */ };
}
```

<br>

## static method

클래스의 static 메서드와 사용 방법이 동일하다. 인터페이스 이름으로 메서드에 접근하여 사용한다.

```java
interface Chef extends Movable, Cookable {
    public default void info() { /* 구현 */ };
    public static void sound() {
        System.out.println("위이잉~~");
    };
}
```

```java
public class RobotTest {
    public static void main(String[] args) {
        Chef.sound();
    }
}
```

위 코드의 출력 결과는 다음과 같다.

```
위이잉~~
```

인터페이스 안에 있는 메서드이지만, static을 붙였다면 위와 같이 구현부를 둘 수 있는 것이다.

<br>

## 인터페이스의 필요성

* 표준화 처리 가능
* 개발 기간 단축 가능
* 서로 관계가 없는 클래스들에게 인터페이스를 통해 관계를 맺을 수 있음.
* 인터페이스를 통한 간접적인 클래스 사용으로 손쉬운 모듈 교체 지원
* 독립적 프로그래밍 가능

<br>

## 인터페이스 vs 클래스

먼저 클래스와 인터페이스를 비교해 보겠다.

<table>
    <tr>
        <th></th>
        <th>클래스</th>
        <th>인터페이스</th>
    </tr>
    <tr>
        <th>특징</th>
        <td>class 키워드를 사용하여 정의 필드와 메서드, 생성자로 이루어짐</td>
        <td>interface 키워드를 사용하여 정의 상수와 추상메서드(메서드 선언부)로 이루어짐. <br>public static final 생략 가능. public abstract 생략 가능.</td>
    </tr>
    <tr>
        <th>관계</th>
        <td>인터페이스를 구현함</td>
        <td>클래스에 의해 구현됨</td>
    </tr>
    <tr>
        <th>멤버 변수</th>
        <td>선언 가능</td>
        <td>상수만 가능</td>
    </tr>
    <tr>
        <th>다중 상속</th>
        <td>클래스는 하나의 클래스만 상속 가능</td>
        <td>인터페이스는 여러 개의 인터페이스 상속 가능 (구현부가 없으므로 헷갈리지 않음)</td>
    </tr>
    <tr>
        <th>다중 구현</th>
        <td>클래스는 여러 개의 인터페이스를 다중으로 구현(implements) 가능</td>
        <td></td>
    </tr>
    <tr>
        <th>인스턴스</th>
        <td>생성 가능</td>
        <td>생성 불가</td>
    </tr>
    <tr>
        <th>타입</th>
        <td>타입으로 사용 가능</td>
        <td>타입으로 사용 가능</td>
    </tr>
</table>

<br>

다음으로, 추상 클래스와 인터페이스를 비교해보겠다.

<table>
    <tr>
        <th></th>
        <th>추상 클래스</th>
        <th>인터페이스</th>
    </tr>
    <tr>
        <th>객체 생성</th>
        <td>불가</td>
        <td>불가</td>
    </tr>
    <tr>
        <th>일반 메서드</th>
        <td>가능</td>
        <td>불가</td>
    </tr>
    <tr>
        <th>일반 필드</th>
        <td>가능</td>
        <td>불가 (상수만 가능)</td>
    </tr>
    <tr>
        <th>메서드</th>
        <td>abstract를 붙여야만 추상 메서드</td>
        <td>모든 메서드는 추상 메서드</td>
    </tr>
    <tr>
        <th>사용</th>
        <td>- 추상적인 클래스의 성격을 가질 때(일부 메서드만 미완성인 설계도)<br>- 서로 유사한 클래스 사이에 코드를 공유하고 싶을 때</td>
        <td>- 서로 관련없는 클래스 사이에 공통으로 적용되는 인터페이스를 구현하기를 원할 때 <br>ex) Comparable, Serializable<br>- 객체(클래스)의 성격이라기보다 어떤 기능을 구현하고 있다는 약속의 성격이 있을 때</td>
    </tr>
    <tr>
        <th>공통점</th>
        <td colspan="2">특정 기능의 구현을 강제하고 싶을 때</td>
    </tr>
</table>