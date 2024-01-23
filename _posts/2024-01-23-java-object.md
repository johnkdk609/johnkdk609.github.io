---
layout: post
title: Object 클래스
categories: Java
date: 2024-01-23 23:53:00 +0900
---
Object 클래스에 대해 알아보겠다.

Object 클래스는 가장 최상위 클래스로 모든 클래스의 조상이다. Object의 멤버는 모든 클래스의 멤버이다.

### toString 메서드

toString 메서드는 객체를 문자열로 변경하는 메서드이다. 기존에 정의되어 있는 toString 메서드의 코드는 다음과 같다.

```java
public String toString() {
    return getCalss().getName() + "0" + Integer.toHexString(hashCode());
}
```

그런데, 우리가 보통 궁금해하는 것은 위와 같은 주소 값이 아니다. 내용이 궁금한 것이다. 그래서 toString 메서드를 다음과 같이 override(재정의) 한다.

```java
@Override
public String toString() {
    return "Student [name=" + name + ", age=" + age + ", major=" + major + "]";
}
```

### equals 메서드

equals 메서드에 대해서도 이전에 다뤘었다. String에서 참조값 비교가 아닌, 문자열의 내용 비교를 위해 사용되었던 메서드이다.

그런데 사실 equals는 두 객체가 같은지 주소 값을 비교하는 메서드이다.

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

위와 같이, 비교 연산자 ```==```로 두 객체의 주소값을 비교하는 메서드인 것이다.

<br>

다음 코드는 두 개의 레퍼런스 변수가 같은 객체를 가리키고 있는지 코드로 실험해본 것이다.

```java
Object obj1 = new Object();
Object obj2 = new Object();
Object obj3 = obj2;

System.out.printf("obj1 == obj2: %b\n", obj1 == obj2);
System.out.printf("obj1 equals obj2: %b\n", obj1.equals(obj2));

System.out.printf("obj2 == obj3: %b\n", obj2 == obj3);
System.out.printf("obj2 equals obj3: %b\n", obj2.equals(obj3));
```

위 코드의 출력 결과는 다음과 같다.

```
obj1 == obj2: false
obj1 equals obj2: false
obj2 == obj3: true
obj2 equals obj3: true
```

obj1과 obj2는 주소값이 같은지 파악했을 때 false가 나오는 것은 자명하다. 그런데 obj1과 obj2를 equals로 보면, 기존에 String의 equals를 생각했으면 true가 나와야 하는데 false가 나온다. 내용이 아니라 주소 값을 비교했기 때문이다. 한편, obj3는 obj2가 가리키는 주소 값을 같이 가리키고 있기 때문에, == 이든 equals 이든 동일하게 true가 나온다.

<br>

그런데, 우리는 equals 메서드로 객체의 주소 값을 비교하고 싶은 것이 아니다. 두 객체의 내용을 비교하고 싶은 것이다.

그러면 equals 메서드를 재정의(override)해야 한다.

### hashCode

객체의 해시 코드는 시스템에서 객체를 구별하기 위해 사용되는 정수값이다.

HashSet, HashMap 등에서 객체의 동일성을 확인하기 위해 사용한다.

equals 메서드를 재정의할 때는 반드시 hashCode도 재정의해야 한다. 미리 작성된 String이나 Number 등에서 재정의 된 hashCode를 활용하는 것이 권장된다.