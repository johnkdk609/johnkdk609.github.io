---
layout: post
title: collect()와 Collectors
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-25 17:09:00 +0900
---
스트림의 최종 연산 중에서 가장 복잡하면서도 유용하게 활용할 수 있는 것이 collect()이다. collect()는 스트림의 기본 요소를 수집하는 최종 연산으로 앞서 배운 리듀싱(reducing)과 유사하다. collect()가 스트림의 요소를 수집하려면, 어떻게 수집할 것인가에 대한 방법이 정의되어 있어야 하는데, 이 방법을 정의한 것이 바로 컬렉터(collector)이다.

컬렉터는 Collector인터페이스를 구현한 것으로, 직접 구현할 수도 있고 미리 작성된 것을 사용할 수도 있다. Collectors클래스는 미리 작성된 다양한 종류의 컬렉터를 반환하는 static메서드를 가지고 있으며, 이 클래스를 통해 제공되는 컬렉터만으로도 많은 일들을 할 수 있다.

> collect() - 스트림의 최종연산. 매개변수로 컬렉터를 필요로 한다.\
> Collector - 인터페이스. 컬렉터는 이 인터페이스를 구현해야 한다.\
> Collectors - 클래스. static메서드로 미리 작성된 컬렉터를 제공한다.

collect()는 매개변수의 타입이 Collector인데, 매개변수가 Collector를 구현한 클래스의 객체이어야 한다는 뜻이다. 그리고 collect()는 이 객체에 구현된 방법대로 스트림의 요소를 수집한다.

(sort() 할 때, Comparator가 필요한 것처럼 collect() 할 때는 Collector가 필요하다.)

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


## 스트림의 통계 - counting(), summingInt()

앞서 살펴보았던 최종 연산들이 제공하는 통계 정보를 collect()로 똑같이 얻을 수 있다. collect()를 사용하지 않고도 쉽게 얻을 수 있는데, 굳이 collect()를 사용하는 방법을 알아보는 이유는 collect()의 사용법을 알아보기 위함이다. 나중에 groupingBy()와 함께 사용할 때 비로소 이 메서드들이 왜 필요한지 알게 될 것이다. 보다 간결한 코드를 위해 Collectors의 static메서드를 호출할 때는 'Collectors.'를 생략하였다. static import가 되어 있도록 가정한다.

(summingInt() 외에도 summingLong(), summingDouble()이 있다. averagingInt()도 마찬가지이다.)

```java
long count = stuStream.count();
long count = stuStream.collect(counting());     // Collectors.counting()

long totalScore = stuStream.mapToInt(Student::getTotalScore).sum();
long totalScore = stuStream.collect(summingInt(Student::getTotalScore));

Optional<Student> topStudent = stuStream.max(Comparator.comparingInt(Student::getTotalScore));
Optional<Student> topStudent = stuStream.collect(maxBy(Comparator.comparingInt(Student::getTotalScore)));

IntSummaryStatistics stat = stuStream.mapToInt(Student::getTotalScore).summaryStatistics();
IntSummaryStatistics stat = stuStream.collect(summarizingInt(Student::getTotalScore));
```

summingInt()와 summarizingInt()를 혼동하지 않도록 주의해야 한다.


## 스트림을 리듀싱 - reducing()

리듀싱 역시 collect()로 가능하다. IntStream에는 매개변수 3개짜리 collect()만 정의되어 있으므로 boxed()를 통해 IntStream을 Stream&#60;Integer&#62;로 변환해야 매개변수 1개짜리 collect()를 쓸 수 있다.

```java
IntStream intStream = new Random().ints(1, 46).distinct().limit(6);

OptionalInt max = intStream.reduce(Integer::max);
Optional<Integer> max = intStream.boxed().collect(reducing(Integer::max));

long sum = intStream.reduce(0, (a, b) -> a + b);
long sum = intStream.boxed().collect(reducing(0, (a, b) -> a + b));

int grandTotal = stuStream.map(Student::getTotalScore).reduce(0, Integer::sum);
int grandTotal = stuStream.collect(reducing(0, Student::getTotalScore, Integer::sum));
```

Collectors.reducing()에는 아래와 같이 3가지 종류가 있다. 세 번째 메서드만 제외하고 reduce()와 같다. 세 번째 것은 위의 예에서 알 수 있듯이 map()과 reduce()를 하나로 합쳐놓은 것 뿐이다.

```java
Collector reducing(BinaryOperator<T> op)
Collector reducing(T identity, BinaryOperator<T> op)
Collector reducing(U identity, Function<T, U> mapper, BinaryOperator<U> op)
```

위의 메서드 목록은 와일드 카드를 제거하여 간략히 한 것이다.


## 스트림을 문자열로 결합 - joining()

joining()은 문자열 스트림의 모든 요소를 하나의 문자열로 연결해서 반환한다. 구분자를 지정해줄 수도 있고, 접두사와 접미사도 지정 가능하다. 스트림의 요소가 String이나 StringBuffer처럼 CharSequence의 자손인 경우에만 결합이 가능하므로 스트림의 요소가 문자열이 아닌 경우에는 먼저 map()을 이용해서 스트림의 요소를 문자열로 변환해야 한다.

```java
String studentNames = stuStream.map(Student::getName).collect(joining());
String studentNames = stuStream.map(Student::getName).collect(joining(","));
String studentNames = stuStream.map(Student::getName).collect(joining(",", "[", "]"));
```

만일 map() 없이 스트림에 바로 joining() 하면, 스트림의 요소에 toString()을 호출한 결과를 결합한다.

```java
// Student의 toString()으로 결합
String studentInfo = stuStream.collect(joining(","));
```