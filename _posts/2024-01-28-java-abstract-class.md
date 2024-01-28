---
layout: post
title: 추상 클래스 (abstract class)
categories: Java
date: 2024-01-28 10:33:00 +0900
---
추상 클래스(abstract class)에 대해 알아보겠다.

클래스는 설계도로 이 설계도를 기반으로 객체를 만든다. 그런데 설계도 중에는 객체 생성이 꼭 필요하지 않은 설계도가 있다. (어떤 설계도로는 객체를 생성하지 않는 것이다.)

가령, Cat, Dog, Person, ... 등등의 클래스들이 Animal 클래스를 상속 받는다고 했을 때, Animal 클래스로는 객체를 생성하지 않을 수 있다.

이 경우에 Animal 클래스는 <b>'추상 클래스'</b>이고, Cat, Dog, Person 클래스들은 '구체 클래스'라고 할 수 있다.

<br>

다음 예시 코드를 보자.

```java
public class KFoodChef {
    String name;
    int age;
    String speciality;

    public void eat() {
        System.out.println("음식을 먹는다.");
    }

    public void cook() {
        System.out.println("한식을 요리한다.");
    }
}
```

```java
public class JFoodChef {
    String name;
    int age;
    String speciality;

    public void eat() {
        System.out.println("음식을 먹는다.");
    }

    public void cook() {
        System.out.println("일식을 요리한다.");
    }
}
```

위와 같이 KFoodChef 클래스와 JFoodChef 클래스가 있을 때, 두 클래스의 코드가 중복되는 부분이 많다. 이때 중복을 제거하기 위해 다음과 같이 Chef 클래스를 생성할 수 있다.

```java
public class Chef {
    String name;
    int age;
    String speciality;

    public void eat() {
        System.out.println("음식을 먹는다.");
    }

    public void cook() {
        System.out.println("전공에 맞는 요리한다.");
    }
}
```

```java
public class KFoodChef extends Chef {
    @Override
    public void cook() {
        System.out.println("한식을 요리한다.");
    }
}
```

```java
public class JFoodChef extends Chef {
    @Override
    public void cook() {
        System.out.println("일식을 요리한다.");
    }
}
```

이전보다 코드가 깔끔해졌다. ```cook()```를 KFoodChef, JFoodChef클래스 각각에서 오버라이드 해서 사용할 수 있는 것이다.

그런데 생각해보면, Chef 클래스로는 직접 객체를 생성하지 않을 것이고, 자손 클래스들이 cook() 메서드를 오버라이드 한다면 굳이 Chef 클래스에 cook() 메서드가 필요한지 의문이다.

<br>

이때 사용할 수 있는 개념이 <b>추상 클래스(abstract class)</b>이다.

cook() 메서드는 자손 클래스에서 반드시 재정의해서 사용되기 때문에 조상의 구현이 무의미하다. <b>메서드는 선언부만 남기고 구현부는 ;(세미콜론)으로 대체</b>하면 된다.

구현부가 없으므로 <b>abstract 키워드를 메서드 선언부에 추가</b>한다. 

또, 객체를 생성할 수 없는 클래스라는 의미로 <b>클래스 선언부에 abstract를 추가</b>한다.

```java
public abstract class Chef {    // 클래스 선언부에 abstract 추가
    String name;
    int age;
    String speicality;

    public void eat() {
        System.out.println("음식을 먹는다.");
    }

    public abstract void cook();    // 구현부 없이 메서드 선언부에 abstract 추가
}
```

<br>

추상 클래스의 특징은 다음과 같다.

* abstract 클래스는 상속 전용의 클래스
* 클래스에 구현부가 없는 메서드가 있으므로 객체를 생성할 수 없음
* 상위 클래스 타입으로 자식을 참조할 수는 있음

```java
// Chef chef1 = new Chef();     // 객체 생성 불가

Chef chef2 = new KFoodChef();   // 참조는 문제 없음
```

* 조상 클래스에서 상속 받은 abstract 메서드를 재정의하지 않은 경우, 클래스 내부에 abstract 메서드가 있으므로 자식 클래스는 abstract 클래스가 되어야 함

<br>

그렇다면 추상 클래스를 사용하는 이유는 무엇일까?

<b>abstract 클래스는 <u>구현의 강제</u>를 통해 프로그램의 안정성을 향상시킬 수 있다</b>는 것이다.