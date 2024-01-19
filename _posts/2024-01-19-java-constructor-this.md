---
layout: post
title: 생성자(constructor)와 this
categories: Java
date: 2024-01-19 23:36:00 +0900
---
자바의 객체에는 생성자(constructor)라는 것이 있다.

객체를 생성할 때 new 키워드와 함께 호출한다.

```java
Person p = new Person();
```

가령 위와 같은 코드가 있을 때, ```new Person()```이 생성자인 것이다.

생성자의 특징들로는 다음과 같은 것들이 있다.

- 클래스명과 동일 (대소문자 전부)
- 결과형 리턴값을 갖지 않음 (void 작성 X)
- 객체가 생성될 때 <b>반드시</b> 하나의 생성자 호출
- 멤버필드의 초기화에 주로 사용
- 하나의 클래스 내부에 생성자가 하나도 없으면 자동적으로 default 생성자가 있는 것으로 인지
    - default 생성자: 매개 변수도 없고 내용도 없는 생성자
- 매개변수의 개수가 다르거나, 자료형이 다른 여러 개의 생성자가 있을 수 있음 (생성자 오버로딩)
- 생성자의 첫 번째 라인으로 <b>this() 생성자</b>를 사용하여 또 다른 생성자를 하나 호출 가능

<br>

기본 생성자가 사용되는 양상은 다음과 같다.

```java
public class Person {
    public Person() {}  // 기본 생성자
}
```

클래스 내에 <u>생성자가 하나도 정의되어 있지 않을 경우 JVM이 자동으로 제공하는 생성자</u>가 기본 생성자이다.

<br>

한편, 파라미터가 있는 생성자도 존재한다.

생성자의 목적이 필드 초기화인데, 생성자 호출 시 값을 넘겨주는 것이다. 매개변수(Parameter)가 있는 생성자를 작성하면 JVM에서 기본 생성자를 추가하지 않는다.

파라미터가 있는 생성자는 다음과 같이 사용된다.

```java
class Person {
    String name;
    int age;

    Person() {} // 기본 생성자 추가

    Person(String name, int age) {  // 매개변수가 있는 생성자
        this.name = name;
        this.age = age;
    }
}
```

```java
public static void main(String[] args) {
    Person p1 = new Person();
    p1.name = "John";
    p1.age = 28;

    Person p2 = new Person("Diane", 26);
}
```

위 코드를 보면, Person 클래스에서 기본 생성자도 작성하고, 매개변수가 있는 생성자도 작성했다. 만약 매개변수가 있는 생성자를 입력할 경우, 기본 생성자는 자동으로 추가되지 않는다. 기본 생성자가 필요하다면 위처럼 따로 추가해놔야 한다.

<br>

생성자는 오버로딩(overloading)이 가능하다.

클래스 내에 메서드 이름이 같고 매개변수의 타입 또는 개수가 다르면 되는 것이다.

```java
class Person {
    Person() {}
    Person(String name) {}
    Person(int age) {}
    Person(String name, int age) {}
}
```

```java
class Main {
    public static void main(String[] args) {
        Person p1 = new Person();
        Person p2 = new Person("John");
        Person p3 = new Person(28);
        Person p4 = new Person("Diane", 26);
    }
}
```

<br>

### this

this는 참조 변수로서, 객체 자신을 가리킨다. this를 이용하여 자신의 멤버에 접근이 가능하다.

지역변수(매개변수)와 필드의 이름이 동일할 경우, 필드임을 식별할 수 있게 한다.

<b>객체 자신에 대한 참조이므로, static 영역에서 this 사용은 불가하다.</b>

즉, 가독성을 증진시키기 위해 this를 사용한다고 이해하면 쉽다.

<br>

this는 다음과 같이 활용된다.

* <b>this.멤버변수</b>

```java
class Person {
    String name;
    int age;
    void info () {
        System.out.println(this.name);
        System.out.println(this.age);
    }
}
```

<br>

또, this는 다음과 같이 활용될 수 있다.

* <b>this([인자값])</b> : 생성자 호출
* this 생성자 호출 시 제한사항
    - <u>생성자 내에서만</u> 호출 가능함
    - 생성자 내에서 <u>첫 번째 구문에 위치해야</u> 함

```java
class Person {
    String name;
    int age;
    Person() {
        this("John");
    }
    Person(String name) {

    }
}
```