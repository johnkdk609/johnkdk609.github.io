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

