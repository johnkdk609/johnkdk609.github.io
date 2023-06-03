---
layout: post
title: 제네릭 타입의 형변환, 제네릭 타입의 제거
description: Java의 정석 기초편 ch. 12 지네릭스, 열거형, 애너테이션
categories: Java
date: 2023-06-03 16:21:00 +09:00
---
제네릭 타입과 원시 타입(primitive type) 간의 형변환이 가능할까?

```java
Box box = null;
Box<Object> objBox = null;

box = (Box)objBox;            // OK. 제네릭 타입 → 원시 타입. 경고 발생
objBox = (Box<Object>)box;    // OK. 원시 타입 → 제네릭 타입. 경고 발생
```

위에서 알 수 있듯이, **제네릭 타입과 넌제네릭(non-generic) 타입 간의 형변환은 항상 가능하다.** 다만 경고가 발생할 뿐이다. 

그러면, 대입된 타입이 다른 제네릭 타입 간에는 형변환이 가능할까?

```java
Box<Object> objBox = null;
Box<String> strBox = null;

objBox = (Box<Object>)strBox;   // ERROR. Box<String> → Box<Object>
strBox = (Box<String>)objBox;   // ERROR. Box<Object> → Box<String>
```

불가능하다. 대입된 타입이 Object일지라도 말이다. 아래의 문장이 안 된다는 얘기는 Box\<String\>이 Box\<Object\>로 형변환될 수 없다는 사실을 간접적으로 알려주는 것이기 때문이다.

```java
Box<Object> objBox = (Box<Object>)new Box<String>();    // ERROR. 형변환 불가능
```

그러면 다음의 문장은 어떨까? Box\<String\>이 Box\<? extends Object\>로 형변환 될까?

```java
Box<? extends Object> wBox = new Box<String>();
```

형변환이 된다. 그래서 전에 배운 makeJuice메서드의 매개변수에 다형성이 적용될 수 있었던 것이다.

```java
// 매개변수로 FruitBox<Fruit>, FruitBox<Apple>, FruitBox<Grape> 등이 가능
static Juice makeJuice(FruitBox<? extends Fruit> box) { ... }

FruitBox<? extends Fruit> box = new FruitBox<Fruit>();    // OK
FruitBox<? extends Fruit> box = new FruitBox<Apple>();    // OK
```


## 제네릭 타입의 제거

컴파일러는 제네릭 타입을 이용해서 소스파일을 체크하고, 필요한 곳에 형변환을 넣어준다. 그리고 제네릭 타입을 제거한다. 즉, 컴파일된 파일(\*.class)에는 제네릭 타입에 대한 정보가 없는 것이다. 이렇게 하는 주된 이유는 제네릭이 도입되기 이전(JDK1.5 이전)의 소스 코드와의 호환성을 유지하기 위해서이다. 

제네릭 타입의 제거 과정은 꽤 복잡하다. 기본적인 제거 과정에 대해서만 살펴보겠다.

### 1. 제네릭 타입의 경계(bound)를 제거한다.

제네릭 타입이 \<T extends Fruit\>라면 T는 Fruit으로 치환된다. \<T\>인 경우는 T는 Object로 치환된다. 그리고 클래스 옆의 제네릭 타입 선언은 제거된다.

```java
class Box<T extends Fruit> {
  void add(T t) {
    ...
  }
}
```
↓
```java
class Box {
  void add(Fruit t) {
    ...
  }
}
```

### 2. 제네릭 타입을 제거한 후에 타입이 일치하지 않으면, 형변환을 추가한다.

List의 get()은 Object타입을 반환하므로 형변환이 필요하다.

```java
T get(int i) {
  return list.get(i);
}
```
↓
```java
Fruit get(int i) {
  return (Fruit)list.get(i);
}
```

와일드 카드가 포함되어 있는 경우에는 다음과 같이 적절한 타입으로의 형변환이 추가된다.

```java
static Juice makeJuice(FruitBox<? extends Fruit> box) {
  String tmp = "";
  for (Fruit f : box.getList()) tmp += f + " ";
  return new Juice(tmp);
}
```
↓
```java
static Juice makeJuice(FruitBox box) {
  String tmp = "";
  Iterator it = box.getList().iterator();
  while (it.hasNext()) {
    tmp += (Fruit)it.next() + " ";
  }
  return new Juice(tmp);
}
```
