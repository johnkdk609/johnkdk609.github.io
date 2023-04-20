---
layout: post
title: 인터페이스(interface)
description: Java의 정석 기초편 ch. 7 객체지향 프로그래밍 2
categories: Java
date: 2023-04-20 11:45:00 +09:00
---
인터페이스(interface)는 일종의 추상 클래스이다. 인터페이스는 추상 클래스처럼 추상 메서드를 갖지만 추상 클래스보다 추상화 정도가 높아서 추상 클래스와 달리 몸통을 갖춘 일반 메서드 또는 멤버변수를 구성원으로 가질 수 없다. **오직 추상 메서드와 상수만을 멤버로 가질 수 있으며, 그 외의 다른 어떠한 요소도 허용하지 않는다. 

추상 클래스를 부분적으로만 완성된 '미완성 설계도'라고 한다면, 인터페이스는 구현된 것은 아무 것도 없고 밑그림만 그려져 있는 '기본 설계도'라 할 수 있다.

인터페이스도 추상 클래스처럼 완성되지 않은 불완전한 것이기 때문에 그 자체만으로 사용되기 보다는 다른 클래스를 작성하는 데 도움을 줄 목적으로 작성된다.

인터페이스를 작성하는 것은 클래스를 작성하는 것과 같다. 다만 키워드로 class 대신 interface를 사용한다는 것만 다르다. 그리고 interface에는 접근 제어자로 public 또는 default 만 사용할 수 있다. 

```java
interface 인터페이스이름 {
  public static final 타입 상수이름 = 값;
  public abstract 메서드이름(매개변수목록);
}
```

일반적인 클래스와 달리 인터페이스의 멤버들은 다음과 같은 제약 사항이 있다.

> - 모든 멤버변수는 public static final이어야 하며, 이를 생략할 수 있다.
> - 모든 메서드는 public abstract이어야 하며, 이를 생략할 수 있다. (단, JDK1.8부터 static 메서드와 디폴트 메서드는 예외)

인터페이스에 정의된 모든 멤버에 예외 없이 적용되는 사항이기 때문에 제어자를 생략할 수 있는 것이며, 편의상 생략하는 경우가 많다. 생략된 제어자는 컴파일 시에 컴파일러가 자동적으로 추가해준다.

```java
interface PlayingCard {
  public static final int SPADE = 4;
  final int DIAMOND = 3;    // public static final int DIAMOND = 3;
  final int HEART = 2;      // public static final int HEART = 2;
  int CLOVER = 1;           // public static final int CLOVER = 1;
  
  public abstract String getCardNumber();
  String getCardKind();     // public abstract String getCardKind();
}
```


### 인터페이스의 상속

인터페이스는 인터페이스로부터만 상속받을 수 있으며, 클래스와는 달리 **다중상속, 즉 여러 개의 인터페이스로부터 상속받는 것이 가능**하다.

(인터페이스는 클래스와 달리 Object 클래스와 같은 최고 조상이 없다.)

```java
interface Movable {
  /** 지정된 위치(x, y)로 이동하는 기능의 메서드 **/
  void move(int x, int y);
}

interface Attackable {
  /** 지정된 대상(u)을 공격하는 기능의 메서드 **/
  void attack(Unit u);
}

interface Fightable extends Movable, Avoidable { }
```

클래스의 상속과 마찬가지로 자손 인터페이스(Fightable)는 조상 인터페이스(Movable, Attackable)에 정의된 멤버를 모두 상속받는다.

그래서 Fightable 자체에는 정의된 멤버가 하나도 없지만 조상 인터페이스로부터 상속받은 두 개의 추상 메서드, move(int x, int y)와 attack(Unit u)을 멤버로 갖게 된다.


### 인터페이스의 구현

인터페이스도 추상 클래스처럼 그 자체로는 인스턴스를 생성할 수 없으며, 추상 클래스가 상속을 통해 추상 메서드를 완성하는 것처럼 인터페이스도 자신에 정의된 추상 메서드의 몸통을 만들어주는 클래스를 작성해야 한다. 그 방법은 추상 클래스가 자신을 상속받는 클래스를 정의하는 것과 다르지 않다. 다만 클래스는 확장한다는 의미의 키워드 'extends'를 사용하지만, 인터페이스는 구현한다는 의미의 키워드 'implements'를 사용한다. 

```java
class 클래스이름 implements 인터페이스이름 {
  // 인터페이스에 정의된 추상 메서드를 모두 구현해야 한다.
}

class Fighter implements Fightable {
  public void move(int x, int y) { /* 내용 생략 */ }
  public void attack(Unit u) { /* 내용 생략 */ }
}
```

(이때 'Fighter 클래스는 Fightable 인터페이스를 구현한다.'라고 표현한다.)

만일 구현하는 인터페이스의 메서드 중 **일부만 구현한다면, abstract를 붙여서 추상 클래스로 선언**해야 한다.

```java
abstract class Fighter implements Fightable {
  public void move(int x, int y) { /* 내용 생략 */ }
}
```

그리고 다음과 같이 상속과 구현을 동시에 할 수도 있다.

```java
class Fighter extends Unit implements Fightable {
  public void move(int x, int y) { /* 내용 생략 */ }
  public void attack(Unit u) { /* 내용 생략 */ }
}
```


### 인터페이스를 이용한 다형성

다형성을 학습할 때 자손 클래스의 인스턴스를 조상 타입의 참조변수로 참조하는 것이 가능하다는 것을 배웠다. 인터페이스 역시 이를 구현한 조상이라 할 수 있으므로 해당 인터페이스 타입의 참조변수로 이를 구현한 클래스의 인스턴스를 참조할 수 있으며, 인터페이스 타입으로의 형변환도 가능하다. 

인터페이스 Fightable을 클래스 Fighter가 구현했을 때, 다음과 같이 Fighter 인스턴스를 Fightable 타입의 참조변수로 참조하는 것이 가능하다.

```java
Fightable f = (Fightable)new Fighter();
```
또는
```java
Fightable f = new Fighter();
```

따라서 인터페이스는 다음과 같이 메서드의 매개변수의 타입으로도 사용될 수 있다.

```java
void attack(Fightable f) {
  // ...
}
```

인터페이스 타입의 매개변수가 갖는 의미는 메서드 호출시 해당 인터페이스를 구현한 클래스의 인스턴스를 매개변수로 제공해야 한다는 것이다.

```java
class Fighter extends Unit implements Fightable {
  public void move(int x, int y) { /* 내용 생략 */ }
  public void attack(Fightable f) { /* 내용 생략 */ }
}
```

위와 같이 Fightable 인터페이스를 구현한 Fighter 클래스가 있을 때, attack 메서드의 매개변수로 Fighter 인스턴스를 넘겨줄 수 있다. 즉, attack(new Fighter())와 같이 할 수 있다는 것이다. 그리고 아래처럼 메서드의 리턴 타입으로 인터페이스를 지정하는 것도 가능하다. 

```java
Fightable method() {
  // ...
  Fighter f = new Fighter();
  return f;                     // 이 두 문장을 한 문장으로 바꾸면 다음과 같다. return new Fighter();
}
```

**리턴 타입이 인터페이스라는 것은 메서드가 해당 인터페이스를 구현한 클래스의 인스턴스를 반환한다는 것을 의미한다.**

위의 코드에서는 method()의 리턴 타입이 Fightable 인터페이스이기 때문에 메서드의 return 문에서 Fightable 인터페이스를 구현한 Fighter 클래스의 인스턴스의 주소를 반환한다.


### 인터페이스의 장점

인터페이스를 사용하는 이유와 그 장점을 정리해보면 다음과 같다.

> - 개발 시간을 단축시킬 수 있다.
> - 표준화가 가능하다.
> - 서로 관계없는 클래스들에게 관계를 맺어줄 수 있다.
> - 독립적인 프로그래밍이 가능하다.

#### 1. 개발 시간을 단축시킬 수 있다.

일단 인터페이스가 작성되면, 이를 사용해서 프로그램을 작성하는 것이 가능하다. 메서드를 호출하는 쪽에서는 메서드의 내용에 관계없이 선언부만 알면 되기 때문이다.
그리고 동시에 다른 한 쪽에서는 인터페이스를 구현하는 클래스를 작성하게 하면, 인터페이스를 구현하는 클래스가 작성될 때까지 기다리지 않고도 양쪽에서 동시에 개발을 진행할 수 있다.

#### 2. 표준화가 가능하다.

프로젝트에 사용되는 기본 틀을 인터페이스로 작성한 다음, 개발자들에게 인터페이스를 구현하여 프로그램을 작성하도록 함으로써 보다 일관되고 정형화된 프로그램의 개발이 가능하다.

#### 3. 서로 관계없는 클래스들에게 관계를 맺어줄 수 있다.

서로 상속 관계에 있지도 않고, 같은 조상 클래스를 가지고 있지 않은 서로 아무런 관계도 없는 클래스들에게 하나의 인터페이스를 공통적으로 구현하도록 함으로써 관계를 맺어줄 수 있다.

#### 4. 독립적인 프로그래밍이 가능하다.

인터페이스를 이용하면 클래스의 선언과 구현을 분리시킬 수 있기 때문에 실제 구현에 독립적인 프로그램을 작성하는 것이 가능하다. 클래스와 클래스 간의 직접적인 관계를 인터페이스를 이용해서 간접적인 관계로 변형하면, 한 클래스의 변경이 관련된 다른 클래스에 영향을 미치지 않는 독립적인 프로그래밍이 가능하다. 
