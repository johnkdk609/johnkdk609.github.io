---
layout: post
title: Generic
categories: Java
date: 2024-02-04 10:32:00 +0900
---
제네릭(Generic)은 다양한 종류의 객체들을 다루는 메서드나 컬렉션 클래스에서 컴파일 시 타입을 체크해주는 기능이다.

타입을 파라미터처럼 사용해서, (타입을) 좀 더 일반화 해서 클래스를 작성하고 싶다는 것이다.

예를 들어 Box를 만든다고 생각해보자.

```java
class Box {
    Object obj;
}
```

위와 같이 멤버 변수에 Object가 들어있는 박스가 있을 수 있다. 이 멤버 변수의 타입이 달라진다고 했을 때, 비슷한 박스인데 타입이 달라진다고 해서 매번 다른 클래스를 만들어야 하느냐이다.

```java
class IntBox {
    int num;
}
```

```java
class StringBox {
    String str;
}
```

지금 보면, 클래스들이 유사하고 타입만 다르다. 이럴 때마다 매번 설계도를 만들어야 할까?

그럴 필요가 없다. 이름을 'Box'라고 하나로 통일하고, 이 타입만 달라지도록 만들 수는 없을 까 하는 취지에서 나온 것이 Generic이다.

이러한 제네릭을 활용할 경우 다음과 같은 장점이 있다.

1. 객체의 타입 안정성을 제공한다.
2. 형변환의 번거로움이 없어지므로 코드가 간결해진다.

<br>

제네릭 클래스는 다음과 같이 정의할 수 있겠다.

클래스를 정의할 때 클래스 안에서 사용되는 자료형(타입)을 구체적으로 명시하지 않고, T와 같이 타입 매개변수를 이용하는 클래스인 것이다.

사용하는 방식은 다음과 같다.

```java
class Box<T> {
    // 생략
}
```
위와 같이 작성해두면, 타입이 다른 Box 클래스를 생성할 때 다음과 같이 활용할 수 있다.

```java
class Box<Red> {
    // 생략
}
```

```java
class Box<Orange> {
    // 생략
}
```

<br>

## 제네릭 클래스 선언

제네릭 클래스를 선언하는 방법은 다음과 같다.

* 클래스 또는 인터페이스 선언 시 &#60;&#62; 에 타입 파라미터 표시

```java
public class ClassName<T> {}
public interface InterfaceName<T> {}
```

<b>&#60;T&#62;</b>의 방식으로 해서 타입 파라미터를 선언한다. 이 T는 그냥 문자일 뿐이다. 'T' 대신 'TypeParameter'을 써도 되는 것이다. 하지만 관습적으로 그러지 않는다.

* 타입 파라미터 → 특별한 의미의 알파벳 보다는 단순히 임의의 참조형 타입을 말함
    * T : reference Type
    * E : Element
    * K : Key
    * V : Value

<br>

다음 코드를 보자.

```java
class Box {
    private Object obj;     // 모든 클래스의 조상 Object

    public Object getObj() {
        return obj;
    }

    public void setObj(Object obj) {
        this.obj = obj;
    }
}
```

타입을 파라미터화하지 않는다면, 타입이 바뀐다고 했을 때 매번 클래스를 다시 만들어야 한다. 그런데 그렇게 하지 않으려고 그냥 Object를 쓰는 것이다. Object는 모든 타입의 조상으로 모든 값이 들어갈 수 있다.

```java
public class BoxText {
    public static void main(String[] args) {
        Box box = new Box();
        box.setObj("Hi");
        box.setObj(11);
        box.setObj(22.1);
    }
}
```

Object 클래스이기 때문에 위와 같이 어떤 것이든 다 넣을 수 있다. 타입별로 설계도를 만들든지, 애초에 타입을 생각하지 않고 Object로 설계도를 만들 수 있는 것이다.

<br>

그런데 이렇게 하면 값을 넣을 때에는 편하지만, 뺄 때는 번거롭다.

왜냐하면, getObj() 했을 때 Object 형으로 반환하기 때문이다. 항상 Object로 반환하기 때문에 형변환을 해줘야 한다.

```java
Object obj = box.getObj();
double d = (double) obj;    // double형으로 casting 필요

// Double은 double 자료형의 Wrapper 클래스
if (obj instanceof Double) {
    System.out.println("실수입니다.");
} else if (obj instanceof String) {
    System.out.println("문자열입니다.");
}
```

<br>

이러한 다양한 문제가 있다. 그래서 타입에 대해 파라미터화 해서 처리할 수 있는 방안에 대해 생각해 보았다.

제네릭 클래스를 만드는 것이다.

```java
// 제네릭 클래스 : 타입을 매개변수화 했다.
// <> : 타입 파라미터
class Box<T> {  // T: String, Double, Integer, ...
    private T t;

    public T getT() {
        return t;
    }

    public void setT(T t) {
        this.t = t;
    }
}

public class BoxTest {
    public static void main(String[] args) {
        Box<Integer> intBox = new Box<Integer>();

        // intBox.setT("Hi");   // 타입 체크를 해준다.
        intBox.setT(11);

        int num = intBox.getT();    // 꺼낼 때 형변환 할 필요가 없다.

        Box<String> strBox = new Box<String>();     // String이 들어갈 수 있는 box.
        strBox.setT("Hi");
        String str = strBox.getT();

        Box<String>[] strBoxArr = new Box[10];  // 배열 생성도 가능
    }
}
```

위와 같이 타입을 매개변수로 넣은 것을 추가하였다. 이제 생성된 intBox는 Integer을 넣고 뺄 수 있는 Box가 되었다.

만약 위 코드에서처럼 ```intBox.setT("Hi");```를 입력하면 타입 체크가 이뤄져 오류가 뜬다.

<br>

## 제네릭 클래스 객체 생성

제네릭 클래스 객체를 생성할 때, 변수와 생성 쪽의 타입은 반드시 일치해야 한다. (상속관계에 있어도 마찬가지)

```java
Box<Student> box = new Box<Student>();  // O
Box<Person> box = new Box<Student>()    // X
```

예를 들어 Person이라는 클래스가 있고, Student 클래스가 Person 클래스를 상속 받고 있다고 하자. 그런데 Box&#60;Person&#62;과 Box&#60;Student&#62; 클래스도 상속 관계에 있을까? 아니다. 이것은 그냥 타입이 다른 다른 클래스가 되는 것이다.

한편, <b>추정이 가능한 경우 타입 생략이 가능</b>하다.

```java
Box<Student> box = new Box<>();     // 생성자 쪽에 <>은 <Student>인데 생략 가능한 것
```

또, 제네릭 타입을 지정하지 않고 생성이 가능하지만 권장되지 않는다. (자동으로 T는 Object가 된다.)

<br>

그냥 Object로 만들 때에는 일일이 형변환 해야 하는 어려움이 있었다. 이것을 해결하기 위해 제네릭이 나온 것이다.

제네릭 클래스로 만들면 각각의 타입에 따라 클래스가 새로 만들어지는 것으로 보면 된다.

```java
class Box<T> {
    private T obj;

    public T getObj() {
        return obj;
    }

    public void setObj(T obj) {
        this.obj = obj;
    }
}
```

```java
public class BoxTest {
    public static void main(String[] args) {
        Box<Person> box1 = new Box<>();     // Person 전용으로 사용하는 것
        box1.setObj(new Person());
        Person p = box1.getObj();
        System.out.println("사람");

        Box<String> box2 = new Box<>();     // String 전용으로 사용하는 것
        box2.setObj("Hi");
        String str = box2.getObj();
        System.out.println("문자열");
    }
}
```

<br>

## 제한된 제네릭 클래스

그런데, 이 T에 모든 타입이 들어가는 것을 원하지 않을 수도 있다.

타입 문자로 사용할 타입을 명시하였지만, 역시 모든 타입을 사용할 수 있는 문제가 발생한다. 이때 제한된 제네릭 클래스를 사용한다.

구체적인 타입의 제한이 필요할 때 extends 키워드를 사용할 수 있다. (Person의 자손만 타입지정 가능)

```java
class Box<T extends Person> {
    private T obj;

    public T getObj() {
        return obj;
    }

    public void setObj(T obj) {
        this.obj = obj;
    }
}
```

위 코드를 보면, ```<T extends Person>```이라고 되어 있다. Person과 그 Person을 상속 받는 자손만 가능하다는 것이다. 그러면 Person의 자손에 대한 로직을 만들 수 있다.

또, ```class Box<T extends Number>```와 같이 입력했다고 가정해보자. Number 클래스는 Integer, Double형 등의 조상 클래스이다. 이를 통해 우리는 타입으로는 Integer나 Double 등만 들어올 수 있다는 것을 예상할 수 있는 것이다. String이나 Person 타입이 들어오는 것을 막을 수 있다.

<br>

클래스를 사용할 때 extends를 썼었는데, <b>인터페이스로 제한할 경우에도 extends 키워드를 사용</b>한다. (implements가 와야 할 것 같지만, 이 경우에는 extends 키워드가 사용되는 것이다.)

또, 클래스와 함께 인터페이스 제약 조건을 이용할 경우 &로 연결한다.

<br>

다음 예시 코드를 보자.

```java
class Box<T extends Number> {   // 제한된 제네릭 클래스 : Number의 자손만 올 수 있음.
    private T t;

    // 계산 로직을 작성할 수 있음.
    public T getT() {
        return t;
    }

    public void setT(T t) {
        this.t = t;
    }
}

public class BoxTest {
    public static void main(String[] args) {

        Box<String> intBox = new Box<>();
        intBox.setT(111);

        // Box<String> strBox = new Box<>();    // Bound mismatch. The type String is not a valid substitute for the bounded parameter <T extends Number> of the type Box<T>
        // strBox.setT("Hello");

        Box<Double> dblBox = new Box<>();
        dblBox.setT(42.21);
    }
}
```

위 코드를 보면, 타입이 Number 클래스와 그 자손들로 제한을 두었다. 그래서 ```Box<String> strBox```을 생성했을 때, 오류가 뜬다.

<br>

이번에는 인터페이스의 에시를 들어보겠다.

```java
public interface AbleToFly {
    void fly();
}
```

```java
public interface AbleToSwim {
    void swim();
}
```

```java
abstract public class Bird implements AbleToFly {

}
```

```java
public class Duct extends Bird implements Duckable {

    @Override
    public void swim() {
        System.out.println("오리는 수영을 합니다.");
    }

    @Override
    public void fly() {
        System.out.println("오리는 날 수도 있어요.");
    }
}
```

```java
public interface Duckable extends AbleToSwim, AbleToFly {

}
```

```java
public class Penguin extends Bird implements AbletoSwim {

    @Override
    public void swim() {
        System.out.println("펭귄은 날 수는 없고 수영만 할 수 있습니다");
    }
}
```

```java
public class Swan extends Bird implements Duckable {

    @Override
    public void swim() {
        System.out.println("백조는 오리처럼 수영도 할 수 있어요.");
    }

    @Override
    public void fly() {
        System.out.println("백조는 오리처럼 날 수도 있어요.");
    }
}
```

```java
class Box<T> {  // 모든 타입에 대해 적용가능한 Box
    T t;
    void setT(T t) {
        this.t = t;
    }

    T getT() {
        return t;
    }
}

class FlyBox<T extends AbleToFly> {     // AbleToFly라는 인터페이스를 구현한 타입만 올 수 있는 Box
    T t;
    void setT(T t) {
        this.t = t;
    }

    T getT() {
        return t;
    }
}

class BirdBox<T extends Bird> {
    T t;
    void setT(T t) {
        this.t = t;
    }

    T getT() {
        return t;
    }
}

// Bird의 자손이면서.. AbleToFly와 AbleToSwim을 모두 구현한 클래스만 올 수 있음.
class FlyingAndSwimmingBirdBox<T extends Bird & AbleToFly & AbleToSwim> {
    T t;
    void setT(T t) {
        this.t = t;
    }

    T getT() {
        return t;
    }
}
```