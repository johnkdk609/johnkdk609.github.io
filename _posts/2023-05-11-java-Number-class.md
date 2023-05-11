---
layout: post
title: Number클래스
description: Java의 정석 기초편 ch. 9 java.lang패키지와 유용한 클래스
categories: Java
date: 2023-05-11 11:36:00 +09:00
---
Number클래스는 추상클래스로, 내부적으로 숫자를 멤버변수로 갖는 래퍼 클래스들의 조상이다. 아래의 그림은 래퍼 클래스의 상속계층도인데, 기본형 중에서 숫자와 관련된 래퍼 클래스들은 모두 Number클래스의 자손이라는 것을 알 수 있다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f4c6a1e5-63c9-4b90-ada4-e3a8becee0f5" width="850" title="[자바의 정석 - 기초편] ch9-25~27 래퍼클래스, Number클래스" alt="래퍼 클래스의 상속 계층도">

그 외에도 Number클래스는 자손으로 BigInteger와 BigDecimal 등이 있는데, BigInteger는 long으로도 다룰 수 없는 큰 범위의 정수를, BigDecimal은 double로도 다룰 수 없는 큰 범위의 부동 소수점수를 처리하기 위한 것으로 연산자의 역할을 대신하는 다양한 메서드를 제공한다. 

참고로 Number클래스의 실제 소스는 아래와 같다. 객체가 가지고 있는 값을 숫자와 관련된 기본형으로 변환하여 반환하는 메서드들을 정의하고 있다.

```java
public abstract class Number implements java.io.Serialzable {
  public abstract int intValue();
  public abstract long longValue();
  public abstract float floatValue();
  public abstract double doubleValue();
  
  public byte byteValue() {
    return (byte)intValue();
  }
  
  public short shortValue() {
    return (short)intValue();
  }
}
```
