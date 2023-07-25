---
layout: post
title: collect()와 Collectors
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-25 17:09:00 +0900
---
스트림의 최종 연산 중에서 가장 복잡하면서도 유용하게 활용할 수 있는 것이 collect()이다. collect()는 스트림의 기본 요소를 수집하는 최종 연산으로 앞서 배운 리듀싱(reducing)과 유사하다. collect()가 스트림의 요소를 수집하려면, 어떻게 수집할 것인가에 대한 방법이 정의되어 있어야 하는데, 이 방법을 정의한 것이 바로 컬렉터(collector)이다.

컬렉터는 Collector인터페이스를 구현한 것으로, 직접 구현할 수도 있고 미리 작성된 것을 사용할 수도 있다. Collectors클래스는 미리 작성된 다양한 종류의 컬렉터를 반환하는 static메서드를 가지고 있으며, 이 클래스를 통해 제공되는 컬렉터만으로도 많은 일들을 할 수 있다.

> collect() - 스트림의 최종연산. 매개변수로 컬렉터를 필요로 한다.
> Collector - 인터페이스. 컬렉터는 이 인터페이스를 구현해야 한다.
> Collectors - 클래스. static메서드로 미리 작성된 컬렉터를 제공한다.

collect()는 매개변수의 타입이 Collector인데, 매개변수가 Collector를 구현한 클래스의 객체이어야 한다는 뜻이다. 그리고 collect()는 이 객체에 구현된 방법대로 스트림의 요소를 수집한다.

(sort() 할 때, Comparator가 필요한 것처럼 collect() 할 때는 Collectot가 필요하다.)

```java
Object collect(Collector collector)     // Collector를 구현한 클래스의 객체를 매개변수로
Object collect(Supplier supplier, BiConsumer accumulator, BiConsumer combiner)
```

그리고 매개변수가 3개나 정의된 collect()는 잘 사용되지는 않지만, Collector인터페이스를 구현하지 않고 간단히 람다식으로 수집할 때 사용하면 편리하다.


## 스트림을 컬렉션, 배열로 변환

스트림의 모든 요소를 컬렉션에 수집하려면, Collectors클래스의 toList()와 같은 메서드를 사용하면 된다. List나 Set이 아닌 특정 컬렉션을 지정하려면, toCollection()에 원하는 컬렉션의 생성자 참조를 매개변수로 넣어주면 된다.

```java
List<String> names = stuStream.map(Student::getName).collect(Collectors.toList());
ArrayList<String> list = names.stream().collect(Collectors.toCollection(ArrayList::new));
```

Map은 키와 값의 쌍으로 저장해야 하므로 객체의 어떤 필드를 키로 사용할지와 값으로 사용할지를 지정해줘야 한다.

```java
Map<String, Person> map = personStream.collect(Collectors.toMap(p -> p.getRegId(), p -> p));
```

위의 문장은 요소의 타입이 Person인 스트림에서 사람의 주민번호(regId)를 키로 하고, 값으로 Person객체를 그대로 저장한다.

(항등 함수를 의미하는 람다식 'p -> p' 대신 Function.identity()를 쓸 수도 있다.)

스트림에 저장된 요소들을 'T[]'타입의 배열로 변환하려면, toArray()를 사용하면 된다. 단, 해당 타입의 생성자 참조를 매개변수로 지정해줘야 한다. 만일 매개변수를 지정하지 않으면 반환되는 배열의 타입은 'Object[]'이다.

```java
Student[] stuNames = studentStream.toArray(Student[]::new);     // OK
Student[] stuNames = studentStream.toArray();       // ERROR
Object[] stuNames = studentStream.toArray();        // OK.
```