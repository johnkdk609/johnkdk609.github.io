---
layout: post
title: 오버라이딩(overriding)
description: Java의 정석 기초편 ch. 7 객체지향 프로그래밍 2
categories: Java
date: 2023-04-13 21:16:00 +09:00
---
조상 클래스로부터 상속받은 메서드의 내용을 변경하는 것을 오버라이딩(overriding)이라고 한다. 상속받은 메서드를 그대로 사용하기도 하지만, 자손 클래스 자신에 맞게 변경해야 하는 경우가 많다. 이때 조상의 메서드를 오버라이딩 한다. 

2차원 좌표계의 한 점을 표현하기 위한 Point 클래스가 있을 때, 이를 조상으로 하는 Point3D 클래스, 3차원 좌표계의 한 점을 표현하기 위한 클래스를 다음과 같이 새로 작성하였다고 하자.

```java
class Point {
  int x;
  int y;
  
  String getLocation() {
    return "x :" + x + ", y :" + y;
  }
}

class Point3D extends Point {
  int z;
  
  String getLocation() {    // 오버라이딩
    return "x :" + x + ", y :" + y + ", z :" + z;
  }
}
```

Point 클래스의 getLocation()은 한 점의 x, y 좌표를 문자열로 반환하도록 작성되었다.

이 두 클래스는 서로 상속관계에 있으므로 Point3D 클래스는 Point 클래스로부터 getLocation()을 상속받지만, Point3D 클래스는 3차원 좌표계의 한 점을 표현하기 위한 것이므로 조상인 Point 클래스로부터 상속받은 getLocation()은 Point3D 클래스에 맞지 않는다. 그래서 이 메서드를 Point3D 클래스 자신에 맞게 z축의 좌푯값도 포함하여 반환하도록 오버라이딩 하였다. 

Point 클래스를 사용하던 사람들은 새로 작성된 Point3D 클래스가 Point 클래스의 자손이므로 Point3D 클래스의 인스턴스에 대해서 getLocation()을 호출하면 Point 클래스의 getLocation()이 그랬듯이 점의 좌표를 문자열로 얻을 수 있을 것이라고 기대할 것이다.

그렇기 때문에 새로운 메서드를 제공하는 것보다 오버라이딩을 하는 것이 바른 선택이다.


### 오버라이딩의 조건

오버라이딩은 메서드의 내용만을 새로 작성하는 것이므로 **메서드의 선언부(메서드 이름, 매개변수, 반환타입)는 조상의 것과 완전히 일치해야** 한다. 다만 접근 제어자(access modifier)와 예외(exception)는 제한된 조건 하에서만 다르게 변경할 수 있다. 

#### 1. 접근 제어자는 조상 클래스의 메서드보다 좁은 범위로 변경할 수 없다.
만일 조상 클래스에 정의된 메서드의 접근 제어자가 protected라면, 이를 오버라이딩하는 자손 클래스의 메서드는 접근 제어자가 protected나 public이어야 한다. 대부분의 경우 같은 범위의 접근 제어자를 사용한다. 접근 제어자의 접근범위를 넓은 것에서 좁은 것 순으로 나열하면 public, protected, (default), private이다.

#### 2. 조상 클래스의 메서드보다 많은 수의 예외를 선언할 수 없다.
아래의 코드를 보면 Child 클래스의 parentMethod()에 선언된 예외의 개수가 조상인 Parent 클래스의 parentMethod()에 선언된 예외의 개수보다 적으므로 바르게 오버라이딩 되었다.

```java
class Parent {
  void parentMethod() throws IOException, SQLException {
    // ...
  }
}

class Child extends Parent {
  void parentMethod() throws IOException {
    // ...
  }
  // ...
}
```

정리하면, 오버라이딩할 때 지켜야 할 조건은 다음과 같다.

> 조상 클래스의 메서드를 자손 클래스에서 오버라이딩할 때\
> **1. 선언부가 조상 클래스의 메서드와 일치해야 한다.**\
> **2. 접근 제어자를 조상 클래스의 메서드보다 좁은 범위로 변경할 수 없다.**\
> **3. 예외는 조상 클래스의 메서드보다 많이 선언할 수 없다.**


### 오버로딩 vs. 오버라이딩

오버로딩과 오버라이딩은 서로 혼동하기 쉽지만 사실 그 차이는 명백하다. 오버로딩은 기존에 없는 새로운 메서드를 추가하는 것이고, 오버라이딩은 조상으로부터 상속받은 메서드의 내용을 변경하는 것이다.

> 오버로딩(overloading) - 기존에 없는 새로운 메서드를 정의하는 것(new)\
> 오버라이딩(overriding) - 상속받은 메서드의 내용을 변경하는 것(change, modify)

아래의 코드를 보고 오버로딩과 오버라이딩을 구별할 수 있어야 한다.

```java
class Parent {
  void parentMethod() {}
}

class Child extends Parent {
  void parentMethod() {}      // 오버라이딩
  void parentMethod(int i) {} // 오버로딩
  
  void childMethod() {}
  void childMethod(int i) {}  // 오버로딩
  void childMethod() {}       // ERROR. 중복 정의 되었음 (already defined in Child)
}
```
