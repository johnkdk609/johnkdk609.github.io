---
layout: post
title: 접근 제한자 (Access Modifier)
categories: Java
date: 2024-01-21 13:55:00 +0900
---
자바의 접근 제한자(access modifier)에 대해서 알아보겠다. 자바의 접근 제한자는 캡슐화(encapsulation)를 하기 위해 사용된다.

### 패키지 (package)

우리는 PC의 많은 파일을 관리하기 위해 폴더를 이용한다. 같은 이치로 프로그램의 많은 클래스를 관리하기 위해서 패키지를 이용한다.

패키지의 특징은 다음과 같다.

- 패키지는 클래스와 관련 있는 인터페이스들을 모아두기 위한 이름 공간
- 패키지의 구분은 .(dot) 연산자를 이용한다.
- 패키지의 이름은 시중에 나와 있는 패키지들과 구분되게 지어야 한다.
- 일반적으로 소속이나 회사의 도메인을 사용한다. ex&#41; ```com.ssafy.project_name.module_name```

<br>

### 임포트 (import)

다른 패키지에 있는 클래스를 사용하기 위해서는 import 과정이 필요하다.

import를 선언할 때에는 import 키워드 뒤에 package 이름과 클래스 이름을 모두 입력하거나, 해당 패키지의 모든 클래스를 포함할 때는 '*'를 사용하기도 한다.

```java
import package_name.class_name;
import package_name.*;
```

<br>

### 캡슐화 (Encapsulation)

캡슐화란 객체의 속성(data fields)과 행위(메서드, methods)를 하나로 묶고, 실제 구현 내용 일부를 외부에 감추어 은닉하는 것이다.

어떤 메서드나 데이터는 외부 객체에서 읽기/쓰기가 가능하지만, 어떤 메서드나 데이터에 대해서는 외부 객체에서 호출하거나 읽기/쓰기를 불가능하게 막아두는 것이다.

다음과 같은 예시를 보자.

```java
public class Person {
    public String name;
    public int age;

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
Person p = new Person("John", 28);

p.age = 60;
p.age = 3;
```

위 코드를 보면, Person 인스턴스 p를 생성했다. 이때 이름은 "John", 나이는 28로 초기화했다. 28살 John의 정보가 등록된 것이다. 그런데, ```p.age = 60;``` 혹은 ```p.age = 3;```으로 John의 나이를 임의로 변경하게 되었다. 데이터가 오염된 것이다.

이러한 상황을 방지하게 해주는 것이 캡슐화(Encapsulation)이다.

<br>

### 접근 제한자 (access modifier)

접근 제한자는 클래스, 멤버 변수, 멤버 메서드 등의 선언부에서 접근 허용 범위를 지정하는 역할의 키워드이다.

접근 제한자의 종류는 4가지가 있다.

1. public
2. protected
3. (default)
4. private

1번에서 4번으로 갈수록 비공개의 요소가 강해진다.

그 외의 제한자로는 다음과 같은 것들이 있다.

- <b>static</b>: 클래스 레벨의 요소 설정
- <b>final</b>: 요소를 더 이상 수정할 수 없게 함 (상수 설정할 때 사용)
- <b>abstract</b>: 추상 메서드 및 추상 클래스 작성

<br>

접근 제한자 4가지에 대해 좀 더 구체적으로 알아보겠다.

* <b>public</b>

모든 위치에서 접근이 가능. 다른 패키지, 다른 클래스, 같은 패키지, 같은 클래스 등... 어디에서나 접근이 가능한 것이다.

* <b>protected</b>

같은 패키지에서만 접근이 가능. 다른 패키지 접근 불가능. 단, 다른 패키지의 클래스와 상속관계가 있을 경우 접근 가능.

즉, 같은 패키지 O, 다른 패키지 X, 다른 패키지 but 상속 O 인 것이다.

* <b>default</b>

같은 패키지에서만 접근이 허용. 접근제한자가 선언이 안 되었을 경우 기본 default가 적용된다.

* <b>private</b>

자신 클래스에서만 접근이 허용됨.

<br>

public, default의 경우 클래스(외부) 사용이 가능하다. 내부클래스, 멤버변수, 메서드는 4가지 모두 사용 가능하다.

<br>

표로 정리해서 보면 다음과 같다.

<table>
    <tr>
        <th>수식어</th>
        <th>클래스 내부</th>
        <th>동일 패키지</th>
        <th style="text-align:center">(다른 패키지 내의)<br>하위 클래스</th>
        <th>다른 패키지</th>
    </tr>
    <tr>
        <th>private</th>
        <td style="text-align:center">O</td>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <th>(default)</th>
        <td style="text-align:center">O</td>
        <td style="text-align:center">O</td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <th>protected</th>
        <td style="text-align:center">O</td>
        <td style="text-align:center">O</td>
        <td style="text-align:center">O</td>
        <td></td>
    </tr>
    <tr>
        <th>public</th>
        <td style="text-align:center">O</td>
        <td style="text-align:center">O</td>
        <td style="text-align:center">O</td>
        <td style="text-align:center">O</td>
    </tr>
</table>

<br>

### 접근자(getter) / 설정자(setter)

캡슐화를 자바에서는 접근자(getter)와 설정자(setter)를 이용해서 구현한다.

클래스에서 선언된 변수 중 접근제한에 의해 접근할 수 없는 변수의 경우 다른 클래스에서 접근할 수 없기 때문에, 접근하기 위한 메서드(설정자와 접근자)를 public으로 선언하여 사용한다.

다음 예시를 보자.

```java
public class Person {
    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

이전과 달리, name과 age를 private으로 감싸놨다. 그리고 이름을 가져오는 getName(), 이름을 설정하는 setName()을 만들어놨다.

멤버 변수는 데이터이다. 인스턴스 각각 고유하게 만들어지는 것이다. 그래서 기본적으로 private으로 막아놓는다. 나 자신만 쓸 수 있게, 외부에서 접근할 수 없게 하는 것이다. 이렇게 되면 나 자신 클래스에서만 사용할 수 있게 된다. 데이터 오염을 방지하는 것이다.

다른 곳에서 값을 변경은 못 하게 막아놓되, 가져다 쓸 수는 있게 한 것이다.

<br>

설정자(setter)가 있다면, 기존에 ```p.age = 3;```과 같이 값을 쉽게 변경할 수 있는 것과 무슨 차이가 있는지 궁금할 수 있다.

이때, 설정자에 조건을 추가할 수 있다.

```java
public void setAge(int age) {
    if (age < 0) {
        System.out.println("나이에 음수를 입력할 수는 없습니다.");
        return;
    }
    this.age = age;
}
```

가령, 위와 같이 음수를 매개변수로 넘겨주면 적용되지 않게 할 수 있는 것이다.

<br>

이러한 접근자, 설정자를 사용하는 방식은 실무에서도 많이 사용된다고 한다.