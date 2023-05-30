---
layout: post
title: 제네릭스(Generics)
description: Java의 정석 기초편 ch. 12 지네릭스, 열거형, 애너테이션
categories: Java
date: 2023-05-30 12:07:00 +09:00
---
제네릭스(Generics)는 다양한 타입의 객체들을 다루는 메서드나 컬렉션 클래스에 컴파일 시의 타입 체크(compile-time type check)를 해주는 기능이다. 객체의 타입을 컴파일 시에 체크하기 때문에 객체의 타입 안정성을 높이고 형변환의 번거로움이 줄어든다. 

예를 들어 ArrayList의 경우 다양한 종류의 객체를 담을 수 있긴 하지만 보통 한 종류의 객체를 담는 경우가 많다. 아래와 같이 ArrayList를 생성할 때, 저장할 객체의 타입을 지정해주면, 지정한 타입 외에 다른 객체가 저장되면 에러가 발생한다.

```java
// Tv객체만 저장할 수 있는 ArrayList를 생성
ArrayList<Tv> tvList = new ArrayList<Tv>();

tvList.add(new Tv());       // OK
tvList.add(new Audio());    // 컴파일 에러. Tv 외에 다른 타입은 저장 불가
```

그리고 저장된 객체를 꺼낼 때는 형변환할 필요가 없어서 편리하다. 이미 어떤 타입의 객체들이 저장되어 있는지 알고 있기 때문이다. 제네릭스를 적용한 코드(보기 2)와 그렇지 않는 코드(보기 1)을 비교해보자.

```java
// 보기 1

ArrayList tvList = new ArrayList();
tvList.add(new Tv());
Tv t = (Tv)tvList.get(0);
```

```java
// 보기 2

ArrayList<Tv> tvList = new ArrayList<Tv>();
tvList.add(new Tv());
Tv t = tvList.get(0);   // 형변환 불필요 
```

정리하면, 제네릭스를 도입함으로써 얻는 장점은 다음과 같다.

> **제네릭스의 장점**
> 1. 타입 안정성을 제공한다.
> 2. 타입 체크와 형변환을 생략할 수 있으므로 코드가 간결해진다.

(타입 안정성을 높인다는 것은 의도하지 않은 타입의 객체를 저장하는 것을 막고, 저장된 객체를 꺼내올 때 원래의 타입과 다른 타입으로 형변환되어 발생할 수 있는 오류를 줄여준다는 말이다.)


## 타입 변수

ArrayList클래스의 선언에서 클래스 이름 옆의 '< >' 안에 있는 E를 '타입 변수(type variable)'라고 하며, 일반적으로는 'Type'의 첫 글자를 따서 T를 사용한다.

그렇다고 타입 변수로 반드시 T를 사용해야 하는 것은 아니며, T가 아닌 다른 것을 사용해도 된다. ArrayList<E>의 경우, 'Element(요소)'의 첫 글자를 따서 타입 변수의 이름으로 E를 사용한다.
