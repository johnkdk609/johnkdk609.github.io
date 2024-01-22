---
layout: post
title: 상속 (Inheritance)
categories: Java
date: 2024-01-22 22:03:00 +0900
---
상속(Inheritance)에 대해 알아보겠다.

상속이란 어떤 클래스의 특성을 그대로 갖는 새로운 클래스를 정의한 것이다. 설계도를 재사용한다고 이해하면 쉽다.

* 설계도 - 상위 클래스 (aka "부모 클래스", "super class")
* 설계도로 만든 설계도 - 하위 클래스 (aka "자식 클래스", "sub class")

<br>

다음 코드를 보자.

```java
public class Person {
    String name;
    int age;

    public void eat() {
        System.out.println("음식을 먹는다.");
    }
}
```

```java
public class Student {
    String name;
    int age;
    String major;

    public void eat() {
        System.out.println("음식을 먹는다");
    }

    public void study() {
        System.out.println("공부를 한다.");
    }
}
```

Person 클래스와 Student 클래스이 코드가 상당부분 겹치는 것을 볼 수 있다. 엄밀히 따지면, Student 클래스가 Person 클래스에 멤버변수와 멤버메서드를 추가한 상황이다.

이때 '상속'을 이용해 Student 클래스를 간략하게 다음과 같이 작성할 수 있다.

```java
public class Student extends Person {
    String major;

    public void study() {
        System.out.println("공부를 한다.");
    }
}
```

상속을 할 때에는 ```extends``` 키워드를 사용한다.

<br>

앞서 상속을 하지 않은 상황에서, main 메서드에 Student 인스턴스 st를 생성한 후, ```st.```을 찍으면 다음과 같이 뜬다.

<img width="449" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f799eeab-9740-4aa2-817f-67f825dfc46b">

위 그림을 보면, 멤버변수 age, major, name 모두 옆에 '- Student'라고 써 있다.

반면, Person 클래스를 상속한 후 Student 인스턴스를 생성하고 ```st.```을 찍으면 다음과 같이 나온다.

<img width="413" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/8c3614a7-a966-4006-941f-49cd0d1b1ddb">

멤버변수 age, name 그리고 멤버메서드 eat() 옆에 '- Person'이라고 써 있다.

상속을 받았기 때문에 멤버변수, 멤버메서드를 전부 다 그대로 가져온 것이다.

그리고 위 그림들을 보면 equals(Object obj), getClass(), hashCode() 등의 메서드들 옆에 '- Object'라고 써 있는 것을 볼 수 있다. 모든 클래스는 Object 클래스를 상속 받는 것이기 때문이다.

<br>

상속의 특징을 정리하면 다음과 같다.

1. 확장성, 재사용성 - 부모의 생성자와 초기화 블럭은 상속 X
2. 클래스 선언 시 <b>extends 키워드</b>를 명시 - <b>다중 상속 불가능</b>, 단일 상속 지원
3. 부모-자식 관계 (Super class - Sub class)
4. 자식 클래스는 부모 클래스의 멤버변수, 멤버메서드를 자신의 것처럼 사용할 수 있다. (<u>단, 접근 제한자에 따라 사용 여부가 달라짐</u>)
5. Object 클래스는 모든 클래스의 조상 클래스이다. - 별도의 extends 선언 없는 클래스는 ```extends Object```가 생략된 것.
6. <b>super 키워드</b>를 통해 조상 클래스의 생성자를 호출한다.
7. <b>오버라이딩</b>(재정의, overriding)

<br>

위 특징들 중, 4번, 6번, 7번에 대해 좀 더 구체적으로 알아보겠다.

우선, 자식 클래스는 부모 클래스의 멤버변수, 멤버메서드를 자신의 것처럼 사용할 수 있으나, 접근 제한자에 따라 사용 여부가 달라진다는 부분이다.

만약 부모 클래스의 멤버변수들을 ```private```으로 막아두었으면, 그것을 상속받는 자식 클래스 입장에서 그냥 접근하는 것은 막혀있다. 대신 getter, setter로 접근할 수는 있다.

```java
// this.id;    // private으로 막혀있기 때문에 접근 불가
// setter을 이용해서 초기화
this.setId(id);     // public 으로 열려 있으므로 접근 가능
```

위 코드와 같이, 직접 ```this.id;```와 같은 방식으로 접근할 수는 없다. 하지만 ```this.setId(id);```와 같은 방식으로는 접근할 수 있는 것이다.

<br>

다음으로 super 키워드에 대해 설명하는 6번이다. ```super()```는 조상의 생성자를 호출한다는 뜻이다. ```super(name, age);```와 같이 인자를 넣어서 사용할 수도 있다.

조상 클래스의 메서드를 호출하는 것도 가능한데, ```super.eat();```와 같은 방식으로 호출하는 것이다.

다음 코드를 보자.

```java
public class Person {
    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void eat() {
        System.out.println("음식을 먹는다.");
    }
}
```

```java
public class Student extends Person {
    String major;

    public Student(String name, int age, String major) {
        super(name, age);
        this.major = major;
    }

    public void study() {
        super.eat();
        System.out.println("공부를 한다.");
    }
}
```

```java
public static void main(String[] args) {
    Student st = new Student("John", 28, "행정학");

    st.study();
}
```

위 코드의 출력 결과는 다음과 같다.

```
음식을 먹는다.
공부를 한다.
```

Person 클래스를 상속받은 Student 클래스의 study()에 ```super.eat();```라고 입력되어 있다. 상위 클래스의 메서드를 호출한 것이다.

<br>

즉, super키워드의 특징은 다음 세 가지이다.

1. 부모클래스의 생성자를 호출할 때
2. 부모의 멤버변수를 호출할 때
3. 명시적으로 부모의 메서드를 호출하는 데 사용 가능

super의 또 중요한 특징 하나는, <u>모든 생성자의 첫줄에 </u>```super();```<u>이 생략되어 있다는 것</u>이다. ```super();``` 도 ```this();``` 처럼 <b>무조건 첫 번째 줄에 와야</b> 한다.

```super();```의 경우 부모 생성자 호출, ```this();```의 경우 자신의 다른 생성자 호출이다. 이럴 때에는 <b>둘 중에 하나만 사용할 수 있다.</b>

<br>

마지막으로 오버라이딩(재정의, Overriding)이다. 만약 내가 특정 부분을 고치고 싶다면 오버라이딩을 할 수 있다. 자식 클래스에서 부모 클래스의 메서드를 재정의하는 것이다.

다음 코드를 보자.

```java
public class Person {
    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void eat() {
        System.out.println("음식을 먹는다.");
    }
}
```

```java
public class Student extends Person {
    String major;

    public Student(String name, int age, String major) {
        super(name, age);
        this.major = major;
    }

    public void study() {
        super.eat();
        System.out.println("공부를 한다.");
    }

    @Override
    public void eat() {
        System.out.println("지식을 먹는다.");
    }
}
```

Student클래스에서 Person의 eat() 메서드의 내용을 재정의한 것이다. "음식을 먹는다." 에서 "지식을 먹는다."로 변경되었다.

어노테이션 ```@Override```가 붙었는데, 이는 컴파일러 주석이다. 이 어노테이션을 붙여주면, 오버라이드 되었음을 컴파일러가 알려준다. 실수를 방지할 수 있게 되는 것이다.

<br>

오버라이딩의 특징을 정리하면 다음과 같다.

* 상위 클래스에 선언된 메서드를 자식 클래스에서 재정의하는 것.
* <b>메서드의 이름, 반환형, 매개변수 (타입, 개수, 순서)가 동일해야 한다.</b>
* <b>하위 클래스의 접근제어자 범위가 상위 클래스보다 크거나 같아야 한다.</b> ex&#41; 부모 클래스의 접근제어자가 public인데 자식 클래스가 private을 해서는 안 되는 것이다.
* <b>조상보다 더 큰 예외를 던질 수 없다.</b> - 부모가 가진 기능을 자식이 없앨 수 없고, 부모보다 자식이 실수를 크게 해서는 안 되는 것이다.
* 메서드 오버로딩(overloading)과 혼동하면 안 된다.