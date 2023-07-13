---
layout: post
title: Object클래스의 메서드 - hashCode()
description: Java의 정석 기초편 ch. 9 java.lang패키지와 유용한 클래스
categories: Java
date: 2023-05-02 13:48:00 +09:00
---
이 메서드는 해싱(hashing) 기법에 사용되는 '해시함수(hash function)'를 구현한 것이다. 해싱은 데이터 관리 기법 중의 하나인데 다량의 데이터를 저장하고 검색하는 데 유용하다.

해시함수는 찾고자 하는 값을 입력하면, 그 값이 저장된 위치를 알려주는 해시코드(hash code)를 반환한다.

일반적으로 해시코드가 같은 두 객체가 존재하는 것이 가능하지만, Object클래스에 정의된 **hashCode메서드**는 객체의 주솟값을 이용해서 해시코드를 만들어 반환하기 때문에 서로 다른 두 객체는 결코 같은 해시코드를 가질 수 없다. 단 64 bit JVM에서는 주소가 64 bit이므로 주소를 해시코드(32 bit)로 변환하면 중복된 값이 나올 수도 있다.

앞서 살펴본 것과 같이 클래스의 인스턴스변수 값으로 객체의 같고 다름을 판단해야 하는 경우라면 equals메서드 뿐만 아니라 hashCode메서드도 적절히 오버라이딩해야 한다. **같은 객체라면 hashCode메서드를 호출했을 때의 결괏값인 해시코드도 같아야 하기 때문이다**. 만일 hashCode메서드를 오버라이딩하지 않는다면 Object클래스에 정의된 대로 모든 객체가 서로 다른 해시코드 값을 가질 것이다. 

```java
class Ex9_3 {
	public static void main(String[] args) {
		String str1 = new String("abc");
		String str2 = new String("abc");

		System.out.println(str1.equals(str2));
		System.out.println(str1.hashCode());
		System.out.println(str2.hashCode());
		System.out.println(System.identityHashCode(str1));
		System.out.println(System.identityHashCode(str2));
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
true
96354
96354
1551870003
967765295
```

String클래스는 문자열의 내용이 같으면, 동일한 해시코드를 반환하도록 hashCode메서드가 오버라이딩 되어 있기 때문에, 문자열의 내용이 같은 str1과 str2에 대해 hashCode()를 호출하면 항상 동일한 해시코드값을 얻는다. 

반면에 System.identityHashCode(Object x)는 Object클래스의 hashCode메서드처럼 객체의 주솟값으로 해시코드를 생성하기 때문에 모든 객체에 대해 항상 다른 해시코드값을 반환할 것을 보장한다. 그래서 str1과 str2가 해시코드는 같지만 서로 다른 객체라는 것을 알 수 있다.
