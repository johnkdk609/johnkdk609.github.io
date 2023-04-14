---
layout: post
title: 조상의 생성자 - super()
description: Java의 정석 기초편 ch. 7 객체지향 프로그래밍 2
categories: Java
date: 2023-04-14 16:10:00 +09:00
---
this()처럼 super()도 생성자이다. this()는 같은 클래스의 다른 생성자를 호출하는 데에 사용되지만, super()는 조상의 생성자를 호출하는 데에 사용된다.

```java
class Point {
  int x, y;
  
  Point(int x, int y) {
    this.x = x;
    this.y = y;
  }
}

class Point3D extends Point {
  int z;
  
  Point3D(int x, int y, int z) {
    this.x = x;   // 조상의 멤버를 초기화
    this.y = y;   // 조상의 멤버를 초기화
    this.z = z;
  }
}
```

위의 코드에서는 Point3D 클래스의 생성자가 조상인 Point 클래스로부터 상속받은 x, y를 초기화 한다. 틀린 코드는 아니지만, 생성자 Point3D()를 아래처럼 조상의 멤버는 조상의 생성자를 통해 초기화되도록 작성하는 것이 바람직하다. 

```java
Point3D(int x, int y, int z) {
  super(x, y);    // 조상 클래스의 생성자 Point(int x, int y)를 호출
  this.z = z;     // 자신의 멤버를 초기화
}
```

이처럼 클래스 자신에 선언된 변수는 자신의 생성자가 초기화를 책임지도록 작성하는 것이 좋다. 참고로 **생성자는 상속되지 않는다.**

```java
public class Ex7_4 {
	public static void main(String[] args) {
		Point3D p = new Point3D(1, 2, 3);
		System.out.println("x=" + p.x + ",y=" + p.y + ",z=" + p.z);
	}
}

class Point {
	int x, y;

	Point(int x, int y) {
		this.x = x;
		this.y = y;
	}
}

class Point3D extends Point {
	int z;

	Point3D(int x, int y, int z) {
		super(x, y);  // Point(int x, int y)를 호출 
		this.z = z;
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
x=1,y=2,z=3
```
