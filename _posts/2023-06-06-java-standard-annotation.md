---
layout: post
title: 표준 어노테이션
description: Java의 정석 기초편 ch. 12 지네릭스, 열거형, 애너테이션
categories: Java
date: 2023-06-06 15:59:00 +0900
---
자바에서 기본적으로 제공하는 어노테이션은 몇 개 없다. 그나마 이들의 일부는 '메타 어노테이션(meta annotation)'으로 어노테이션을 정의하는 데 사용되는 어노테이션의 어노테이션이다.

<table>
  <tr>
    <th>annotation</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>@Override</td>
    <td>컴파일러에게 메서드를 오버라이딩하는 것이라고 알린다.</td>
  </tr>
  <tr>
    <td>@Deprecated</td>
    <td>앞으로 사용하지 않을 것을 권장하는 대상에 붙인다.</td>
  </tr>
  <tr>
    <td>@SuppressWarnings</td>
    <td>컴파일러의 특정 경고메시지가 나타나지 않게 해준다.</td>
  </tr>
  <tr>
    <td>@SafeVarargs</td>
    <td>제네릭스 타입의 가변인자에 사용한다.(JDK1.7)</td>
  </tr>
  <tr>
    <td>@FunctionalInterface</td>
    <td>함수형 인터페이스라는 것을 알린다.(JDK1.8)</td>
  </tr>
  <tr>
    <td>@Native</td>
    <td>native메서드에서 참조되는 상수 앞에 붙인다.(JDK1.8)</td>
  </tr>
  <tr>
    <td>@Target&#42;</td>
    <td>어노테이션이 적용 가능한 대상을 지정하는 데 사용한다.</td>
  </tr>
  <tr>
    <td>@Documented&#42;</td>
    <td>어노테이션 정보가 javadoc으로 작성된 문서에 포함되게 한다.</td>
  </tr>
  <tr>
    <td>@Inherited&#42;</td>
    <td>어노테이션이 자손 클래스에 상속되도록 한다.</td>
  </tr>
  <tr>
    <td>@Retention&#42;</td>
    <td>어노테이션이 유지되는 범위를 지정하는 데 사용한다.</td>
  </tr>
  <tr>
    <td>@Repeatable&#42;</td>
    <td>어노테이션을 반복해서 적용할 수 있게 한다.(JDK1.8)</td>
  </tr>
</table>
▲ 표 12-1 자바에서 기본적으로 제공하는 표준 어노테이션(&#42;가 붙은 것은 메타 어노테이션)

메타 어노테이션을 제외한 어노테이션에 대해서 먼저 알아보겠다.


## @Override

메서드 앞에만 붙일 수 있는 어노테이션으로, 조상의 메서드를 오버라이딩하는 것이라는 것을 컴파일러에게 알려주는 역할을 한다. 아래의 코드에서처럼 오버라이딩 할 때 조상 메서드의 이름을 잘못 써도 컴파일러는 이것이 잘못된 것인지 알지 못한다.

```java
class Parent {
  void parentMethod() { }
}

class Child extends Parent {
  void parentmethod() { }   // 오버라이딩하려 했으나 실수로 이름을 잘못 적음
}
```

오버라이딩할 때는 이처럼 메서드의 이름을 잘못 적는 경우가 많은데, 컴파일러는 그저 새로운 이름의 메서드가 추가된 것으로 인식할 뿐이다. 게다가 실행 시에도 오류가 발생하지 않고 조상의 메서드가 호출되므로 어디서 잘못되었는지 알아내기 어렵다.

```java
// 보기 1

class Child extends Parent {
  void parentmethod() {}
}
```
↓
```java
// 보기 2

class Child extends Parent {
  @Override
  void parentmethod() {}
}
```

그러나 위의 보기 2 코드와 같이 메서드 앞에 '@Override'를 붙이면, 컴파일러가 같은 이름의 메서드가 조상에 있는지 확인하고 없으면, 에러메시지를 출력한다.

오버라이딩할 때 메서드 앞에 '@Override'를 붙이는 것이 필수는 아니지만, 알아내기 어려운 실수를 미연에 방지해주므로 반드시 붙이는 것이 좋다.

```java
class Parent {
	void parentMethod() { }
}

class Child extends Parent {
	@Override
	void parentmethod() { } // 조상 메서드의 이름을 잘못 적었음 
}
```

```java
Ex12_7.java:6: error: method does not override or implement a method from a supertype
    @Override
    ^
1 error
```

이 예제를 컴파일하면 위와 같은 에러메시지가 나타난다. 오버라이딩을 해야 하는데 하지 않았다는 것이다. '@Override'를 메서드 앞에 붙이지 않았다면 나타나지 않았을 메시지이다. Child클래스에서 메서드의 이름을 'parentMethod'로 변경하고 다시 컴파일 하면, 에러메시지가 나타나지 않는다. 


## @Deprecated

새로운 버전의 JDK가 소개될 때, 새로운 기능이 추가될 뿐만 아니라 기존의 부족했던 기능들을 개선하기도 한다. 이 과정에서 기존의 기능을 대체할 것들이 추가되어도, 이미 여러 곳에서 사용되고 있을지 모르는 기존의 것들을 함부로 삭제할 수 없다.

그래서 생각해낸 방법이 더 이상 사용되지 않는 필드가 메서드에 '@Deprecated'를 붙이는 것이다. 이 어노테이션이 붙은 대상은 다른 것으로 대체되었으니 더 이상 사용하지 않을 것을 권한다는 의미이다. 예를 들어 java.util.Date클래스의 대부분의 메서드에는 '@Deprecated'가 붙어있는데, Java API에서 Date클래스의 getDate()를 보면 아래와 같이 적혀있다.

```java
int getDate()
  Deprecated.
  As of JDK version 1.1, replaced by Calendar.get(Calendar.DAY_OF_MONTH).
```

이 메서드 대신에 JDK1.1부터 추가된 Calendar클래스의 get()을 사용하라는 얘기이다. 기존의 것 대신 새로 추가된 개선된 기능을 사용하도록 유도하는 것이다. 굳이 기존의 것을 사용하겠다면, 아무도 못 말리겠지만 가능하면 '@Deprecated'가 붙은 것들은 사용하지 않아야 한다.

```java
class NewClass {
  @Deprecated
  int oldField;
  
  @Deprecated
  int getOldField() { return oldField; };
}
```

만일 '@Deprecated'가 붙은 대상을 사용하는 코드를 작성하면, 컴파일할 때 아래와 같은 메시지가 나타난다.

```java
Note: AnnotationEx2.java uses or overrides a deprecated API.
Note: Recompile with -Xlint:deprecation for details.
```

해당 소스파일(AnnotationEx2.java)이 'deprecated'된 대상을 사용하고 있으며, '-Xlint:deprecation'옵션을 붙여서 다시 컴파일하면 자세한 내용을 알 수 있다는 뜻이다.

```java
C:\jdk1.8\wordk\ch12>javac -Xlint:deprecation AnnotationEx2.java
AnnotationEx2.java21: warnings: [deprecation] oldField in NewClass has been deprecated
  nc.oldField = 10;
                ^
```


## @FunctionalInterface

'함수형 인터페이스(functional interface)'를 선언할 때, 이 어노테이션을 붙이면 컴파일러가 '함수형 인터페이스'를 올바르게 선언했는지 확인하고, 잘못된 경우 에러를 발생시킨다. 필수는 아니지만, 붙이면 실수를 방지할 수 있으므로 '함수형 인터페이스'를 선언할 때는 이 어노테이션을 반드시 붙여야 한다. 

(함수형 인터페이스는 추상 메서드가 하나뿐이어야 한다는 제약이 있다.)

```java
@FunctionalInterface
public interface Runnable {
  public abstract void run();   // 추상 메서드
}
```


## @SuppressWarnings

컴파일러가 보여주는 경고메시지가 나타나지 않도록 억제해준다. 이전 예제에서처럼 컴파일러의 경고메시지는 무시하고 넘어갈 수도 있지만, 모두 확인하고 해결해서 컴파일 후에 어떠한 메시지도 나타나지 않게 해야 한다.

그러나 경우에 따라서는 경고가 발생할 것을 알면서도 묵인해야 할 때가 있는데, 이 경고를 그대로 놔두면 컴파일할 때마다 메시지가 나타난다. 이전 예제에서 확인한 것과 같이 '-Xlint'옵션을 붙이지 않으면 컴파일러는 경고의 자세한 내용은 보여주지 않으므로 다른 경고들을 놓치기 쉽다. 이때는 묵인해야 하는 경고가 발생하는 대상에 반드시 '@SuppressWarnings'를 붙여서 컴파일 후에 어떤 경고 메시지도 나타나지 않게 해야 한다. 

'@SuppressWarnings'로 억제할 수 있는 경고 메시지의 종류는 여러 가지가 있는데, JDK의 버전이 올라가면서 계속 추가될 것이다. 이 중에서 주로 사용되는 것은 "deprecation", "unchecked", "rawtypes", "varargs" 정도이다.

"deprecation"은 앞서 살펴본 것과 같이 '@Deprecated'가 붙은 대상을 사용해서 발행하는 경고를, "unchecked"는 제네릭스로 타입을 지정하지 않았을 때 발생하는 경고를, "rawtypes"는 제네릭스를 사용하지 않아서 발생하는 경고를, 그리고 "varargs"는 가변인자의 타입이 제네릭 타입일 때 발생하는 경고를 억제할 때 사용한다. 

억제하려는 경고 메시지를 어노테이션의 뒤에 괄호() 안에 문자열로 지정하면 된다.

```java
@SuppressWarnings("unchecked")      // 제네릭스와 관련된 경고를 억제
ArrayList list = new ArrayList();   // 제네릭 타입을 지정하지 않았음.
list.add(obj);                      // 여기서 경고가 발생하지만 억제됨
```

만일 둘 이상의 경고를 동시에 억제하려면 다음과 같이 해야 한다. 배열에서처럼 괄호{}를 추가로 사용해야 한다는 것에 주의해야 한다.

```java
@SuppressWarnings({ "deprecation", "unchecked", "varargs" })
```
