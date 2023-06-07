---
layout: post
title: 메타 어노테이션
description: Java의 정석 기초편 ch. 12 지네릭스, 열거형, 애너테이션
categories: Java
date: 2023-06-07 15:47:00 +0900
---
메타 어노테이션(meta annotation)은 '어노테이션을 위한 어노테이션', 즉 어노테이션에 붙이는 어노테이션으로 어노테이션을 정의할 때 어노테이션의 적용대상(target)이나 유지기간(retention) 등을 지정하는 데 사용된다.

(메타 어노테이션은 'java.lang.annotation'패키지에 포함되어 있다.)

<table>
  <tr>
    <th>어노테이션</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>@Target</td>
    <td>어노테이션이 적용가능한 대상을 지정하는 데 사용한다.</td>
  </tr>
  <tr>
    <td>@Documented</td>
    <td>어노테이션 정보가 javadoc으로 작성된 문서에 포함되게 한다.</td>
  </tr>
  <tr>
    <td>@Inherited</td>
    <td>어노테이션이 자손 클래스에 상속되도록 한다.</td>
  </tr>
  <tr>
    <td>@Retention</td>
    <td>어노테이션이 유지되는 범위를 지정하는 데 사용한다.</td>
  </tr>
  <tr>
    <td>@Repeatable</td>
    <td>어노테이션을 반복해서 적용할 수 있게 한다.(JDK1.8)</td>
  </tr>
</table>


## @Target

어노테이션이 적용가능한 대상을 지정하는 데 사용된다. 아래는 '@SuppressWarnings'를 정의한 것인데, 이 어노테이션에 적용할 수 있는 대상을 '@Target'으로 지정하였다. 앞서 언급한 것과 같이 여러 개의 값을 지정할 때는 배열에서처럼 괄호{}를 사용해야 한다. 

```java
@Target({ TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE })
@Retention(RetentionPolicy.SOURCE)
public @interface SuppressWarnings {
  String[] value();
}
```

'@Target'으로 지정할 수 있는 어노테이션의 적용대상의 종류는 아래와 같다.

<table>
  <tr>
    <th>대상 타입</th>
    <th>의미</th>
  </tr>
  <tr>
    <td>ANNOTATION_TYPE</td>
    <td>어노테이션</td>
  </tr>
  <tr>
    <td>CONSTRUCTOR</td>
    <td>생성자</td>
  </tr>
  <tr>
    <td>FIELD</td>
    <td>필드(멤버변수, enum상수)</td>
  </tr>
  <tr>
    <td>LOCAL_VARIABLE</td>
    <td>지역변수</td>
  </tr>
  <tr>
    <td>METHOD</td>
    <td>메서드</td>
  </tr>
  <tr>
    <td>PACKAGE</td>
    <td>패키지</td>
  </tr>
  <tr>
    <td>PARAMETER</td>
    <td>매개변수</td>
  </tr>
  <tr>
    <td>TYPE</td>
    <td>타입(클래스, 인터페이스, enum)</td>
  </tr>
  <tr>
    <td>TYPE_PARAMETER</td>
    <td>타입 매개변수(JDK1.8)</td>
  </tr>
  <tr>
    <td>TYPE_USE</td>
    <td>타입이 사용되는 모든 곳(JDK1.8)</td>
  </tr>
</table>

'TYPE'은 타입을 선언할 때 어노테이션을 붙일 수 있다는 뜻이고, 'TYPE_USE'는 해당 타입의 변수를 선언할 때 붙일 수 있다는 뜻이다. 위의 표의 값들은 'java.lang.annotation.ElementType'이라는 열거형에 정의되어 있으며, 아래와 같이 static import문을 쓰면 'ElementType.TYPE'를 'TYPE'과 같이 간단히 할 수 있다. 

```java
import static java.lang.annotation.ElementType.*;

@Target({ FIELD, TYPE, TYPE_USE })    // 적용대상이 FIELD, TYPE, TYPE_USE
public @interface MyAnnotation { }    // MyAnnotation을 정의

@MyAnnotation     // 적용대상이 TYPE인 경우
class MyClass {
  @MyAnnotation   // 적용대상이 FIELD인 경우
  int i;
  
  @MyAnnotation   // 적용대상이 TYPE_USE인 경우
  myClass mc;
}
```


## @Retention

어노테이션이 유지(retention)되는 기간을 지정하는 데 사용된다. 어노테이션의 유지 정책(retention policy)의 종류는 다음과 같다.

<table>
  <tr>
    <th>유지 정책</th>
    <th>의미</th>
  </tr>
  <tr>
    <td>SOURCE</td>
    <td>소스 파일에만 존재. 클래스파일에는 존재하지 않음.</td>
  </tr>
  <tr>
    <td>CLASS</td>
    <td>클래스 파일에 존재. 실행시에 사용불가. 기본값</td>
  </tr>
  <tr>
    <td>RUNTIME</td>
    <td>클래스 파일에 존재. 실행시에 사용가능.</td>
  </tr>
</table>

'@Override'나 '@SuppressWarnings'처럼 컴파일러가 사용하는 어노테이션은 유지 정책이 'SOURCE'이다. 컴파일러를 직접 작성할 것이 아니면, 이 유지정책은 필요 없다.

```java
@Target(ElementTpye.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {}
```

유지 정책을 'RUNTIME'으로 하면, 실행 시에 '리플렉션(reflection)'을 통해 클래스 파일에 저장된 어노테이션의 정보를 읽어서 처리할 수 있다. '@FunctionalInterface'는 '@Override'처럼 컴파일러가 체크해주는 어노테이션이지만, 실행 시에도 사용되므로 유지 정책인 'RUNTIME'으로 되어 있다.

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface FunctionalInterface {}
```

유지 정책 'CLASS'는 컴파일러가 어노테이션의 정보를 클래스 파일에 저장할 수 있게는 하지만, 클래스 파일이 JVM에 로딩될 때는 어노테이션의 정보가 무시되어 실행 시에 어노테이션에 대한 정보를 얻을 수 없다. 이것이 'CLASS'가 유지정책의 기본값임에도 불구하고 잘 사용되지 않는 이유이다. 

(지역 변수에 붙은 어노테이션은 컴파일러만 인식할 수 있으므로, 유지정책이 'RUNTIME'인 어노테이션을 지역 변수에 붙여도 실행 시에 인식되지 않는다.)


## @Documented

어노테이션에 대한 정보가 javadoc으로 작성한 문서에 포함되도록 한다. 자바에서 제공하는 기본 어노테이션 중에 '@Override'와 '@SuppressWarnings'를 제외하고는 모두 이 메타 어노테이션이 붙어 있다.

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface FunctionalInterface {}
```


## @Inherited

어노테이션이 자손 클래스에 상속되도록 한다. '@Inherited'가 붙은 어노테이션을 조상 클래스에 붙이면, 자손 클래스도 이 어노테이션이 붙은 것과 같이 인식된다.

```java
@Inherited                        // @SuperAnno가 자손까지 영향 미치게
@interface SuperAnno {}

@SuperAnno
class Parent {}

class Child extends Parent {}     // Child에 어노테이션이 붙은 것으로 인식
```

위의 코드에서 Child클래스는 어노테이션이 붙지 않았지만, 조상인 Parent클래스에 붙은 '@SuperAnno'가 상속되어 Child클래스에도 '@SuperAnno'가 붙은 것처럼 인식된다.


## @Repeatable

보통은 하나의 대상에 한 종류의 어노테이션을 붙이는데, '@Repeatable'이 붙은 어노테이션은 여러 번 붙일 수 있다.


```java
@Repeatable(ToDos.class)    // ToDo어노테이션을 여러 번 반복해서 쓸 수 있게 한다.
@interface ToDo {
  String value();
}
```

예를 들어 '@ToDo'라는 어노테이션이 위와 같이 정의되어 있을 때, 다음과 같이 My Class클래스에 '@ToDo'를 여러 번 붙이는 것이 가능하다.

```java
@ToDo("delete test codes.")
@ToDo("override inherited methods")
class MyClass {
  ...
}
```

일반적인 어노테이션과 달리 같은 이름의 어노테이션이 하나의 대상에 여러 번 적용될 수 있기 때문에, 이 어노테이션들을 하나로 묶어서 다룰 수 있는 어노테이션도 추가로 정의해야 한다.

```java
@interface ToDos {    // 여러 개의 ToDo어노테이션을 담을 컨테이너 어노테이션 ToDos
  ToDo[] value();     // ToDo어노테이션 배열타입의 요소를 선언. 이름이 반드시 value이어야 함
}

@Repeatable(ToDos.class)    // 괄호 안에 컨테이너 어노테이션을 지정해줘야 한다.
@interface ToDo {
  String value();
}
```
