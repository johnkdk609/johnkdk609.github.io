---
layout: post
title: 모든 어노테이션의 조상, 마커 어노테이션, 어노테이션 요소의 규칙
description: Java의 정석 기초편 ch. 12 지네릭스, 열거형, 애너테이션
categories: Java
date: 2023-06-09 15:50:00 +0900
---
모든 어노테이션의 조상은 Annotation이다. 그러나 어노테이션은 상속이 허용되지 않으므로 아래와 같이 명시적으로 Annotation을 조상으로 지정할 수 없다.

```java
@interface TestInfo extends Annotation {    // ERROR. 허용되지 않는 표현
  int count();
  String testedBy();
    ...
}
```

게다가 아래의 소스에서 볼 수 있듯이 Annotation은 어노테이션이 아니라 일반적인 인터페이스로 정의되어 있다.

```java
package java.lang.annotation;

public interface Annotation {   // Annotation 자신은 인터페이스이다.
  boolean equals(Object obj);
  int hashCode();
  String toString();
  
  Class<? extends Annotation> annotationType();   // 어노테이션의 타입을 반환
}
```

모든 어노테이션의 조상인 Annotation인터페이스가 위와 같이 정의되어 있기 때문에, 모든 어노테이션 객체에 대해 equals(), hashCode(), toString()과 같은 메서드를 호출하는 것이 가능하다.

```java
Class<AnnotationTest> cls = AnnotationTest.class;
Annotation[] annoArr = cls.getAnnotations();

for (Annotation a : annoArr) {
  System.out.println("toString():"+a.toString());
  System.out.println("hashCode():"+a.hashCode());
  System.out.println("equals():"+a.equals(a));
  System.out.println("annotationType():"+a.annotationType());
}
```

위의 코드는 AnnotationTest클래스에 적용된 모든 어노테이션에 대해 toString(), hashCode(), equals()를 호출한다.


## 마커 어노테이션

값을 지정할 필요가 없는 경우, 어노테이션의 요소를 하나도 정의하지 않을 수 있다. Serializable이나 Cloneable인터페이스처럼, 요소가 하나도 정의되지 않은 어노테이션을 마커 어노테이션이라고 한다.

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {}   // 마커 어노테이션. 정의된 요소가 하나도 없다.

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Test {}       // 마커 어노테이션. 정의된 요소가 하나도 없다.
```


## 어노테이션 요소의 규칙

어노테이션의 요소를 선언할 때 반드시 지켜야 하는 규칙은 다음과 같다.

> - 요소의 타입은 기본형, String, enum, 어노테이션, Class만 허용된다.
> - () 안에 매개변수를 선언할 수 없다.
> - 예외를 선언할 수 없다.
> - 요소를 타입 매개변수로 정의할 수 없다.

다음의 코드에서 무엇이 잘못되었는지 찾아보자.

```java
@interface AnnoTest {
  int id = 100;
  String major(int i, int j);
  String minor() throws Exception;
  ArrayList<T> list();
}
```

아래의 코드에 적힌 주석을 보고 자신의 생각과 비교해 보자.

```java
@interface AnnoTest {
  int id = 100;                     // OK. 상수 선언. static final int id = 100;
  String major(int i, int j);       // ERROR. 매개변수를 선언할 수 없음
  String minor() throws Exception;  // ERROR. 예외를 선언할 수 없음
  ArrayList<T> list();              // ERROR. 요소의 타입에 타입 매개변수 사용 불가
}
```


## 어노테이션의 활용 예제

```java
import java.lang.annotation.*;

@Deprecated
@SuppressWarnings("1111") // 유효하지 않은 애너테이션은 무시된다.
@TestInfo(testedBy="aaa", testDate=@DateTime(yymmdd="160101",hhmmss="235959"))
class Ex12_8 {
	public static void main(String args[]) {
		// Ex12_8의 Class객체를 얻는다.
		Class<Ex12_8> cls = Ex12_8.class;

		TestInfo anno = cls.getAnnotation(TestInfo.class);
		System.out.println("anno.testedBy()="+anno.testedBy());
		System.out.println("anno.testDate().yymmdd()=" +anno.testDate().yymmdd());
		System.out.println("anno.testDate().hhmmss()=" +anno.testDate().hhmmss());

		for(String str : anno.testTools())
			System.out.println("testTools="+str);

		System.out.println();

		// Ex12_8에 적용된 모든 애너테이션을 가져온다.
		Annotation[] annoArr = cls.getAnnotations();

		for(Annotation a : annoArr)
			System.out.println(a);
	} // main의 끝
}

@Retention(RetentionPolicy.RUNTIME)  // 실행 시에 사용가능하도록 지정 
@interface TestInfo {
	int       count()	  	default 1;
	String    testedBy();
	String[]  testTools() 	default "JUnit";
	TestType  testType()    default TestType.FIRST;
	DateTime  testDate();
}

@Retention(RetentionPolicy.RUNTIME)  // 실행 시에 사용가능하도록 지정
@interface DateTime {
	String yymmdd();
	String hhmmss();
}

enum TestType { FIRST, FINAL }
```

위 코드의 출력 결과는 다음과 같다.

```
anno.testedBy()=aaa
anno.testDate().yymmdd()=160101
anno.testDate().hhmmss()=235959
testTools=JUnit

@java.lang.Deprecated()
@TestInfo(count=1, testType=FIRST, testTools=[JUnit], testedBy=aaa, testDate=@DateTime(yymmdd=160101, hhmmss=235959))
```

어노테이션을 직접 정의하고, 어노테이션의 요소의 값을 출력하는 방법을 보여주는 예제이다. Ex12_8클래스에 적용된 어노테이션을 실행시간에 얻으려면, 아래와 같이 하면 된다.

```java
Class<Ex12_8> cls = Ex12_8.class;
TestInfo anno = (TestInfo)cls.getAnnotation(TestInfo.class);
```

'Ex12_8.class'는 클래스 객체를 의미하는 리터럴이다. 앞서 9장에서 배운 것과 같이, 모든 클래스 파일은 클래스 로더(class loader)에 의해 메모리에 올라갈 때, 클래스에 대한 정보가 담긴 객체를 생성하는데 이 객체를 클래스 객체라고 한다. 이 객체를 참조할 때는 '클래스이름.class'의 형식을 사용한다. 

클래스 객체에는 해당 클래스에 대한 모든 정보를 가지고 있는데, 어노테이션의 정보도 포함되어 있다.

클래스 객체가 가지고 있는 getAnnotation()이라는 메서드에 매개변수로 정보를 얻고자 하는 어노테이션을 지정해주거나 getAnnotations()로 모든 어노테이션을 배열로 받아올 수 있다.

```java
TestInfo anno = (TestInfo)cls.getAnnotation(TestInfo.class);
System.out.println("anno.testedBy()="+anno.testedBy());

// AnnotationEx5에 적용된 모든 어노테이션을 가져온다.
Annotation[] annoArr = cls.getAnnotations();
```

(Class클래스를 Java API에서 찾아보면 클래스의 정보를 제공하는 다양한 메서드가 정의되어 있는 것을 확인할 수 있다.)
