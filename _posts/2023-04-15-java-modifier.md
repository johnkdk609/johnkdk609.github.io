---
layout: post
title: 제어자(modifier) - static, final, abstract
description: Java의 정석 기초편 ch. 7 객체지향 프로그래밍 2
categories: Java
date: 2023-04-15 17:37:00 +09:00
---
제어자(modifier)는 클래스, 변수 또는 메서드의 선언부에 함께 사용되어 부가적인 의미를 부여한다. 제어자의 종류는 크게 접근 제어자와 그 외의 제어자로 나눌 수 있다.

> **접근 제어자** - public, protected, (default), private\
> **그 외** - static, final, abstract, native, transient, synchronized, volatile, strictfp

제어자는 클래스나 멤버변수와 메서드에 주로 사용되며, 하나의 대상에 대해서 여러 제어자를 조합하여 사용하는 것이 가능하다.

단, 접근 제어자는 한 번에 네 가지 중 하나만 선택해서 사용할 수 있다. 즉, 하나의 대상에 대해서 public과 private를 함께 사용할 수 없다는 것이다.

(제어자들 간의 순서는 관계 없지만, 주로 접근 제어자를 제일 왼쪽에 놓는 경향이 있다.)

접근 제어자 외에도 여러 제어자가 있으나, static, final, abstract를 제외하고는 자주 사용되지 않는다.


### static - 클래스의, 공통적인

static은 '클래스의' 또는 '공통적인'의 의미를 가지고 있다. 인스턴스 변수는 하나의 클래스로부터 생성되었더라도 각기 다른 값을 유지하지만, 클래스 변수(static 멤버변수)는 인스턴스에 관계 없이 같은 값을 가진다. 그 이유는 하나의 변수를 모든 인스턴스가 공유하기 때문이다. 

static이 붙은 멤버변수와 메서드, 그리고 초기화 블럭은 인스턴스가 아닌 클래스에 관계된 것이기 때문에 인스턴스를 생성하지 않고도 사용할 수 있다.

인스턴스 메서드와 static 메서드의 근본적인 차이는 메서드 내에서 인스턴스 멤버를 사용하는가의 여부에 있다.

> static이 사용될 수 있는 것 - 멤버변수, 메서드, 초기화 블럭

<table>
  <tr>
    <th>제어자</th>
    <th>대상</th>
    <th>의미</th>
  </tr>
  <tr>
    <td rowspan="2">static</td>
    <td style="text-align:center">멤버변수</td>
    <td>- 모든 인스턴스에 공통적으로 사용되는 클래스 변수가 된다.<br>- 클래스 변수는 인스턴스를 생성하지 않고도 사용 가능하다.<br>- 클래스가 메모리에 로드될 때 생성된다.</td>
  </tr>
  <tr>
    <td style="text-align:center">메서드</td>
    <td>- 인스턴스를 생성하지 않고도 호출이 가능한 static 메서드가 된다.<br>- static 메서드 내에서는 인스턴스 멤버들을 직접 사용할 수 없다.</td>
  </tr>
</table>

인스턴스 멤버를 사용하지 않는 메서드는 static을 붙여서 static 메서드로 선언하는 것을 고려해보도록 하자. 가능하다면 static 메서드로 하는 것이 인스턴스를 생성하지 않고도 호출이 가능해서 더 편리하고 속도도 더 빠르다.

```java
class StaticTest {
  static int width = 200;   // 클래스 변수(static 변수)
  static int height = 120;  // 클래스 변수(static 변수)
  
  static {                  // 클래스 초기화 블럭
    // static 변수의 복잡한 초기화 수행
  }
  
  static int max(int a, int b) {    // 클래스 메서드(static 메서드)
    return a > b ? a : b;
  }
}
```


### final - 마지막의, 변경될 수 없는

final은 '마지막의' 또는 '변경될 수 없는'의 의미를 가지고 있으며 거의 모든 대상에 사용될 수 있다.

변수에 사용되면 값을 변경할 수 없는 상수가 되며, 메서드에 사용되면 오버라이딩을 할 수 없게 되고, 클래스에 사용되면 자신을 확장하는 자손 클래스를 정의하지 못하게 된다.

> final이 사용될 수 있는 곳 - 클래스, 메서드, 멤버변수, 지역변수

<table>
  <tr>
    <th>제어자</th>
    <th>대상</th>
    <th>의미</th>
  </tr>
  <tr>
    <td rowspan="4" style="text-align:center">final</td>
    <td style="text-align:center">클래스</td>
    <td>변경될 수 없는 클래스, 확장될 수 없는 클래스가 된다.<br>그래서 final로 지정된 클래스는 다른 클래스의 조상이 될 수 없다.</td>
  </tr>
  <tr>
    <td style="text-align:center">메서드</td>
    <td>변경될 수 없는 메서드. final로 지정된 메서드는 오버라이딩을 통해 재정의될 수 없다.</td>
  </tr>
  <tr>
    <td style="text-align:center">멤버변수</td>
    <td rowspan="2">변수 앞에 final이 붙으면, 값을 변경할 수 없는 상수가 된다.</td>
  </tr>
  <tr>
    <td style="text-align:center">지역변수</td>
  </tr>
</table>

(final 클래스의 대표적인 예는 String과 Math 이다.)

```java
final class FinalTest {       // 조상이 될 수 없는 클래스
  final int MAX_SIZE = 10;    // 값을 변경할 수 없는 멤버변수(상수)
  
  final void getMaxSize() {   // 오버라이딩할 수 없는 메서드(변경 불가)
    final int LV = MAX_SIZE;  // 값을 변경할 수 없는 지역변수(상수)
    return MAX_SIZE;
  }
}
```

### abstract - 추상의, 미완성의

abstract는 '미완성'의 의미를 갖고 있다. 메서드의 선언부만 작성하고 실제 수행 내용은 구현하지 않은 추상 메서드를 선언하는 데에 사용된다.

그리고 클래스에 사용되어 클래스 내에 추상 메서드가 존재한다는 것을 쉽게 알 수 있게 한다.

> abstract가 사용될 수 있는 곳 - 클래스, 메서드

<table>
  <tr>
    <th>제어자</th>
    <th>대상</th>
    <th>의미</th>
  </tr>
  <tr>
    <td rowspan="2" style="text-align:center">abstract</td>
    <td style="text-align:center">클래스</td>
    <td>클래스 내에 추상 메서드가 선언되어 있음을 의미한다.</td>
  </tr>
  <tr>
    <td style="text-align:center">메서드</td>
    <td>선언부만 작성하고 구현부는 작성하지 않은 추상 메서드임을 알린다.</td>
  </tr>
</table>

```java
abstract class AbstractTest {   // 추상 클래스(추상 메서드를 포함한 클래스)
  abstract void move();         // 추상 메서드(구현부가 없는 메서드)
}
```

**추상 클래스는 아직 완성되지 않은 메서드가 존재하는 '미완성 설계도'이므로 인스턴스를 생성할 수 없다.**

```java
AbstractTest a = new AbstractTest();    // ERROR. 추상 클래스의 인스턴스 생성 불가
```
