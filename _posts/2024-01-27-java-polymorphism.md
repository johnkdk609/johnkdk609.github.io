---
layout: post
title: 다형성 (Polymorphism)
categories: Java
date: 2024-01-27 18:16:00 +0900
---
다형성(Polymorphism)에 대해 알아보겠다.

다형성이란 '많은 형상을 가질 수 있는 성질'로, <b>상속관계에 있을 때 조상 클래스의 타입으로 자식 객체를 참조할 수 있다</b>는 개념이다.

<br>

다음 코드를 보자.

```java
public class Person {
    String name;
    int age;

    Person() {}

    Person(String name) {
        this.name = name;
    }

    public void eat() {
        System.out.println("음식을 먹는다.");
    }
}
```

```java
public class Student extends Person {
    String major;
    public void study() {
        System.out.println("공부를 한다.");
    }
}
```

위와 같이 Person 클래스가 있고, Person 클래스를 상속받는 Student 클래스가 있다.

이때 다형성은 다음과 같은 방식으로 구현된다.

```java
Person p = new Student("John", 28, "행정학");
```

조상 클래스의 타입인 Person으로 자식 객체를 가리키고 있는 것이다. 실제 객체는 Student인데, 조상 클래스 타입으로 참조하고 있기 때문에, Person 부분만 따로 떼어서 본다고 이해하면 된다. <b>메모리에 실제로 올라가 있는 것은 Student 타입</b>이다.

당연히 최고 조상인 Object 타입으로 Student 객체를 참조할 수 있다.

<br>

위 코드에 대해 메모리상의 모습을 보면 다음과 같다.

```new Student();```를 선언하여 Student 객체를 생성하면, heap 영역에 Student 클래스의 ```major, study()```의 멤버변수, 메서드가 올라간다. 그리고 Student 클래스의 조상인 Person의 ```name, age, eat()```가 올라간다. 그리고 마지막으로 최고 조상인 Object의 ```equals(), toString(), ...``` 등이 올라간다.

이때 ```Object obj = new Student();```라고 다형성을 적용하면, Student 객체의 속성들 중 Object의 속성들만 접근할 수 있게 된다.

마찬가지로 ```Person p = new Student();```로 다형성을 적용하면, Student 객체의 속성들 중 Person의 속성들만 접근할 수 있게 된다.

<br>

반면, 자식 클래스 타입으로 조상 객체를 가리키는 것은 안 된다.

```java
// Student st = new Person("Diane", 26);    // 에러.
```

<br>

### 참조변수의 형 변환

다형성 개념에서 자손타입에서 조상타입으로 변환되면 <b>묵시적 형 변환</b>이 이뤄진다. 형 변환을 생략할 수 있는 것이다.

코드를 보면 다음과 같다. (Student 클래스는 Person 클래스를 상속 받았다.)

```java
Student st = new Student();

Person p = st;  // 묵시적 형 변환
```

<br>

반면, 조상타입에서 자손타입으로 변환되면 <b>명시적 형 변환</b>이 이뤄진다고 볼 수 있다. <u>형 변환을 생략할 수 없다.</u>

명시적 형 변환은 오류가 나더라도 발생하면 개발하는 사람이 책임을 져야 한다.

```java
Person p = new Person();

Student st = (Student) p;   // 명시적 형 변환
```

위 상황에서 ```st.eat();```을 호출하든, ```st.study();```를 호출하든 다음과 같은 메세지가 콘솔에 뜬다.

<img width="1690" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f12a53e7-18b9-462e-a9c8-2db3095f376e">

'class polymorphism.Person cannot be cast to class polymorphism.Student' 즉 조상 클래스인 Person이 자손 클래스인 Student로 cast 될 수 없다는 것이다. (물론 명시적 형 변환을 하면 일단 코드 상에서는 문제가 없어 보일 수는 있다.)

메모리에는 Person 객체까지밖에 안 올라갔기 때문에 이러한 오류가 발생한다. 실제 객체는 사람인데 이것을 학생으로 착각한 것이다.

<br>

한편, 다음과 같이 처음부터 다형성을 적용해 명시적 형 변환을 했을 때는 얘기가 다르다.

```java
Person p = new Student();

Student st = (Student) p;   // 명시적 형 변환
```

위 상황에서도 물론 Student 인스턴스가 생성된 것이지만, 처음에 Person 클래스가 참조하고 있었기 때문에 나중에 Student 클래스가 참조하려면 명시적 형 변환을 해야 한다.

그런데 이때 ```st.eat();``` 혹은 ```st.study();```를 호출하면 에러 없이 잘 작동한다. 메모리 상(heap 영역)에 올라간 인스턴스가 Student 인스턴스이기 때문이다.

<br>

### instanceof 연산자

instanceof 연산자는 참조변수가 참조하고 있는 인스턴스의 타입을 확인하기 위해서 사용된다. 결과를 boolean으로 반환하는데, true가 반환되면 해당 타입으로 형 변환이 가능한 것이다.

```java
Person person = new Person();

if (person instanceof Student) {
    Student student = (Student) person;
    student.study();
}
```

<br>

참조 변수와 인스턴스 멤버의 관계에 대해 알아보면 다음과 같다.

```java
class Parent {
    String x = "parent";

    public String method() {
        return "parent method~!";
    }
}
```

```java
class Child extends Parent {
    String x = "child";

    @Override
    public String method() {
        return "child method~!";
    }
}
```

```java
public class BindingTest {
    public static void main(String[] args) {
        Parent p = new Child();
        Child c = new Child();

        System.out.println(p.x);
        System.out.println(p.method());

        System.out.println(c.x);
        System.out.println(c.method());
    }
}
```

위 코드의 실행 결과는 다음과 같다.

```
parent
child method~!
child
child method~!
```

이 경우에는 Parent의 멤버변수 x가 Child의 멤버변수 x로 오버라이드 된 것으로 보면 안 된다. ```Parent p = new Child();```로 처음부터 Child 인스턴스가 생성된 것이기 때문이다. 이것은 숨긴다는 의미로 <b>'숨김(hiding)'</b>이라고 한다.

'숨김(hiding)'은 자바에서 변수와 메서드를 오버라이딩(overriding)하는 경우에 발생하는 현상이다. 부모 클래스와 자식 클래스 간에 같은 이름의 변수가 메서드가 있을 때, 자식 클래스에서 해당 변수나 메서드를 새롭게 정의하면 부모 클래스의 것을 가려버리는데, 이를 숨김이라고 하는 것이다.

위 코드를 봤을 때, Parent 클래스와 Child 클래스 모두 'x'라는 변수와 'method()'라는 메서드를 가지고 있다. 하지만 Child 클래스에서 'x' 변수와 'method()' 메서드를 다시 정의했다. 따라서 Child 클래스에서는 부모 클래스의 'x' 변수와 'method()' 메서드를 가리고 있는 것이다.

이러한 <b>숨김은 변수와 메서드에 대한 접근을 할 때 참조 변수의 타입에 따라 결정된다.</b> 즉, 위의 코드에서 ```Parent p = new Child();```와 같이 부모 클래스의 참조 변수로 자식 클래스의 객체를 참조할 때, 'p'를 통해 접근하는 변수와 메서드는 부모 클래스의 것이 아니라 자식 클래스에서 숨겨진 것들을 가리키게 된다.

그러면 ```System.out.println(p.x);```의 출력 결과가 왜 ```parent```인지 궁금할 수 있다. 실행 시점에 참조되는 변수는 참조 변수의 타입에 따라 결정된다. 'p'는 Parent 클래스의 참조 변수이므로 ```p.x```는 부모 클래스인 Parent의 변수 'x'에 접근한다. 따라서 ```parent```가 출력되는 것이다.

Child 클래스에서 새롭게 정의된 변수 'x'는 부모 클래스의 변수 'x'를 가리지 않는다. 대신에, <b>부모 클래스의 'x'와 같은 이름의 변수 'x'를 자식 클래스에서 새롭게 정의했기 때문에 숨김(hiding)이 발생한다. 이렇게 숨겨진 변수는 부모 클래스의 참조 변수로 접근할 때에만 가려진 변수가 참조될 수 있다.</b> 따라서 'p'를 통해 접근할 때는 부모 클래스의 변수 'x'가 출력되고, 'c'를 통해 접근할 때는 자식 클래스의 변수 'x'가 출력된다.

<br>

### 참조 변수와 인스턴스 변수의 관계

상속 관계에서 멤버변수가 중복되면 참조 변수의 타입에 따라 연결이 달라진다. <b>(hiding)</b>

메서드가 중복될 때 무조건 자식 클래스의 메서드가 호출된다. <b>(Overriding)</b>

static 메서드는 참조변수 타입의 영향을 받기 때문에 이를 방지하고자 클래스 이름으로 메서드를 호출한다.

<br>

이러한 다형성의 개념은 다른 타입의 객체를 다루는 배열에서 활용될 수 있겠다.