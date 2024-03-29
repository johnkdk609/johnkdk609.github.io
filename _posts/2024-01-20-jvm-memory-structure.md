---
layout: post
title: JVM 메모리 구조
categories: Java
date: 2024-01-20 23:39:00 +0900
---
JVM 메모리 구조에 대해 알아보겠다.

Java 언어는 메모리 관리를 개발자가 하지 않는다. GC(Garbage Collection)가 메모리 관리를 한다.

<br>

<b>Garbage Collection</b>

* Heap 영역 (class 영역 포함)에 생성된 메모리 관리 담당
* 더 이상 사용되지 않는 객체들을 점검하여 제거
* 자동적 실행됨 (CPU가 한가한지, 메모리가 부족한지...)
* JVM에 의해서 실행됨

실제로는 ```System.gc()```를 통해 호출할 수 있지만, 시스템에 영향을 주기 때문에 하지 않는 것이 권장된다.

<br>

자바 파일이 생성되고 실행되는 과정을 메모리 차원에서 보면 다음과 같이 진행된다.

우선 ```.java```파일을 작성한다. 그러면 컴파일러가 바이트코드로 바꿔준다. (```.class```파일) 그리고 실행하면 JVM 위에 올라간다.

컴파일을 하면 ```.class``` 파일을 Class Loader이 읽어온다.

JVM 메모리에는 3개의 영역이 있다.

1. 메서드 영역 (static 영역)
2. 힙(heap) 영역
3. 스택(stack) 영역

첫 번째, 메서드 영역이다. 클래스와 관련된 정보를 저장하는 영역으로, 설계도가 올라간다고 생각하면 된다. "static영역", "클래스 영역"의 이름으로 불리기도 한다.

두 번째, 힙(heap) 영역이다. 인스턴스가 생성되는 공간으로, 생성자를 통해 인스턴스를 생성하면 힙 영역에 들어간다. 이때 인스턴스는 여러개일 수 있다.

세 번째, 스택(stack) 영역이다. 메서드를 수행할 때 프레임이 이 스택 영역에 차곡차곡 쌓인다. LIFO (Last In First Out). 실행을 하면 먼저 main method가 프레임 단위로 쌓인다. main 메서드 실행에 필요한 변수 등이 저장된다. 그리고 main 메서드가 다른 메서드를 호출하면, 그 위에 다른 메서드가 올라간다. 그리고 그 메서드가 실행이 되면 스택 영역에서 빠져나가고, 마지막에 main 메서드가 실행되고 끝나는 것이다. 필요한 변수나, 중간 결과 값을 임시로 기억하는 곳이고, 메서드 종료 시 할당 메모리가 제거된다는 특징을 가지고 있다.

<br>

정리하자면, 메서드 영역은 클래스와 관련된 정보가 저장되고, 힙 영역에는 인스턴스가 생성되며, 스택 영역에서는 메서드 수행되는 것들이 스택으로 할당되는 것이다. (함수는 스택 영역에서 실행된다.)

<br>

예시로 보면 다음과 같다.

```java
Person p1 = new Person();
p1.name = "Yang";
p1.age = 45;
p1.hobby = "Youtube";
```

Person이라는 클래스(설계도)가 클래스 영역에 로드 된다. Person 설계도에는 name, age, hobby가 있고, 이전에 생성했던 info(), study() 등이 있는데 이것들이 클래스 영역에 로드 된다.

다음으로 스택 영역에 main 함수가 쌓인다. main 함수에서 p1이라는 변수를 만들었다. 인스턴스가 생성된 것이다.

생성된 인스턴스 p1은 힙 영역에 들어간다. 이 인스턴스는 가령 name이 있어야 한다. 위 코드를 보면 기본 생성자로 호출이 되었기 때문에, 제일 처음에는 ```name = null; age = 0; hobby = null;```로 초기화되어 있다.

이 인스턴스의 주소가 0x001이라고 가정했을 때, 이 주소값이 p1에 저장되어 있다. (스택의 main 영역에 p1의 주소값이 저장되어 있는 것이다.)

이제 ```p1.name = "Yang";```으로 name에 값을 넣고 있다. 메인 메서드에서 ```p1.name = "Yang";```으로 넣고 있으니, 힙 영역에서 "Yang"의 주소값을 name이 가리키게 된다.

<br>

### static 특징

1. 로딩 시점
- static: 클래스 로딩 시
- non-static: 객체 생성 시

2. 메모리상의 차이
- static: 클래스당 하나의 메모리 공간만 할당
- non-static: 인스턴스 당 메모리가 별도로 할당

3. 문법적 특징
- static: 클래스 이름으로 접근
- non-static: 객체 생성 후 접근

```java
public class Person {
    static int pCount;

    String name;
    int age;
    String hobby;
}
```

```java
public class PersonTest {
    public static void main(String[] args) {
        Person p = new Person();
        p.name = "Kim";

        Person.pCount++;

        p.pCount++;     // 오류는 나지 않지만 경고 메세지 뜬다.
    }
}
```

4. static 영역에서는 non-static 영역을 직접 접근이 불가능

```java
public class Main {
    String str = "문장";

    public static void main(String[] args) {
        System.out.println(str);    // Non-static field 'str' cannot be referenced from a static context
    }
}
```

인스턴스가 만들어져야지만 접근 가능한 공간이기 때문이다. 다른 세계라고 보면 된다.

그러나 반대로 non-static영역에서 static 영역에는 접근 가능하다.

5. non-static 영역에서는 static 영역에 접근이 가능

```java
public class Main {
    static String str = "문장";

    public void print() {
        System.out.println(str);    // 문제 없음
    }
}
```