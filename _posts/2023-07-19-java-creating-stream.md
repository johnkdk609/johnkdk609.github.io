---
layout: post
title: 스트림 만들기
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-19 16:49:00 +0900
---
스트림으로 작업을 하려면, 스트림이 필요하니까 일단 스트림을 생성하는 방법부터 먼저 알아보겠다. 스트림의 소스가 될 수 있는 대상은 배열, 컬렉션, 임의의 수 등 다양하며, 이 다양한 소스들로부터 스트림을 생성하는 방법에 대해 배우게 될 것이다.


## 스트림 만들기 - 컬렉션

컬렉션의 최고 조상인 Collection에 stream()이 정의되어 있다. 그래서 Collection의 자손인 List와 Set을 구현한 컬렉션 클래스들은 모두 이 메서드로 스트림을 생성할 수 있다. stream()은 해당 컬렉션을 소스(source)로 하는 스트림을 반환한다.

```java
Stream<T> Collection.stream()
```

예를 들어 List로부터 스트림을 생성하는 코드는 다음과 같다.

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);  // 가변인자
Stream<Integer> intStream = list.stream();          // list를 소스로 하는 스트림 생성
```

forEach()는 지정된 작업을 스트림의 모든 요소에 대해 수행한다. 아래의 문장은 스트림의 모든 요소를 화면에 출력한다.

```java
intStream.forEach(System.out::println);     // 스트림의 모든 요소를 출력한다.
intStream.forEach(System.out::println);     // ERROR. 스트림이 이미 닫혔다.
```

한 가지 주의할 점은 forEach()가 스트림의 요소를 소모하면서 작업을 수행하므로 같은 스트림에 forEach()를 두 번 호출할 수 없다는 것이다. 그래서 스트림의 요소를 한 번 더 출력하려면 스트림을 새로 생성해야 한다. forEach()에 의해 스트림의 요소가 소모되는 것이지, 소스의 요소가 소모되는 것은 아니기 때문에 같은 소스로부터 다시 스트림을 생성할 수 있다.


## 스트림 만들기 - 배열

배열을 소스로 하는 스트림을 생성하는 메서드는 다음과 같이 Stream과 Arrays에 static메서드로 정의되어 있다.

```java
Stream<T> Stream.of(T... values)    // 가변 인자
Stream<T> Stream.of(T[])
Stream<T> Arrays.stream(T[])
Stream<T> Arrays.stream(T[] array, int startInclusive, int endExclusive)
```

예를 들어 문자열 스트림은 다음과 같이 생성한다.

```java
Stream<String> strStream = Stream.of("a", "b", "c");    // 가변 인자
Stream<String> strStream = Stream.of(new String[]{"a", "b", "c"});
Stream<String> strStream = Arrays.stream(new String[]{"a", "b", "c"});
Stream<String> strStream = Arrays.stream(new String[]{"a", "b", "c"}, 0, 3);
```

그리고 int, long, double과 같은 기본형 배열을 소스로 하는 스트림을 생성하는 메서드도 있다.

```java
IntStream IntStream.of(int... values)   // Stream이 아니라 IntStream
IntStream IntStream.of(int[])
IntStream Arrays.stream(int[])
IntStream Arrays.stream(int[] array, int startInclusive, int endExclusive)
```

이 외에도 long과 double타입의 배열로부터 LongStream과 DoubleStream을 반환하는 메서드들이 있다.


## 스트림 만들기 - 임의의 수

난수를 생성하는 데 사용하는 Random클래스에는 아래와 같은 인스턴스 메서드들이 포함되어 있다. 이 메서드들은 해당 타입의 난수들로 이루어진 스트림을 반환한다.

```java
IntStream ints()
LongStream longs()
DoubleStream doubles()
```

이 메서드들이 반환하는 스트림은 크기가 정해지지 않은 '무한 스트림(infinite stream)'이므로 limit()도 같이 사용해서 스트림의 크기를 제한해 주어야 한다. limit()은 스트림의 개수를 지정하는 데 사용되며, 무한 스트림을 유한 스트림으로 만들어 준다.

```java
IntStream intStream = new Random().ints();          // 무한 스트림
intStream.limit(5).forEach(System.out::println);    // 5개의 요소만 출력한다.
```

아래의 메서드들은 매개변수로 스트림의 크기를 지정해서 '유한 스트림'을 생성해서 반환하므로 limit()을 사용하지 않아도 된다.

```java
IntStream ints(long streamSize)
LongStream longs(long streamSize)
DoubleStream doubles(long streamSize)

IntStream intStream = new Random().ints(5);     // 크기가 5인 난수 스트림을 반환
```

위 메서드들에 의해 생성된 스트림의 난수는 아래의 범위를 갖는다.

```java
Integer.MIN_VALUE <= ints() <= Integer.MAX_VALUE
Long.MIN_VALUE <= longs() <= Long.MAX_VALUE
0.0 <= doubles() <= 1.0
```


## 스트림 만들기 - 특정 범위의 정수

IntStream과 LongStream은 다음과 같이 지정된 범위의 연속된 정수를 스트림으로 생성해서 반환하는 range()와 rangeClosed()를 가지고 있다.

```java
IntStream IntStream.range(int begin, int end)
IntStream IntStream.rangeClosed(int begin, int end)
```

range()의 경우 경계의 끝인 end가 범위에 포함되지 않고, rangeClosed()의 경우는 포함된다.

```java
IntStream intStream = IntStream.range(1, 5);        // 1, 2, 3, 4
IntStream intStream = IntStream.rangeClosed(1, 5);  // 1, 2, 3, 4, 5
```

int보다 큰 범위의 스트림을 생성하려면 LongStream에 있는 동일한 이름의 메서드를 사용하면 된다.

지정된 범위(begin~end)의 난수를 발생시키는 스트림을 얻는 메서드는 아래와 같다. 단, end는 범위에 포함되지 않는다.

```java
IntStream ints(int begin, int end)
LongStream longs(long begin, long end)
DoubleStream doubles(double begin, double end)

IntStream ints(long streamSize, int begin, int end)
LongStream longs(long streamSize, long begin, long end)
DoubleStream doubles(long streamSize, double begin, double end)
```


## 스트림 만들기 - 람다식 iterate(), generate()

Stream클래스의 iterate()와 generate()는 람다식을 매개변수로 받아서, 이 람다식에 의해 계산되는 값들을 요소로 하는 무한 스트림을 생성한다.

```java
static <T> Stream<T> iterate(T seed, UnaryOperator<T> f)
static <T> Stream<T> generate(Supplier<T> s)
```

iterate()는 씨앗값(seed)으로 지정된 값부터 시작해서, 람다식 f에 의해 계산된 결과를 다시 seed값으로 해서 계산을 반복한다. 아래의 evenStream은 0부터 시작해서 값이 2씩 계속 증가한다.

```java
Stream<Integer> evenStream = Stream.iterate(0, n -> n + 2);     // 0, 2, 4, 6, ...
```

<table>
    <tr>
        <th>
            n -> n + 2
        </th>
    </tr>
    <tr>
        <td text="center">
            0 -> 0 + 2<br>2 -> 2 + 2<br>4 -> 4 + 2<br>...
        </td>
    </tr>
</table>

generate()도 iterate()처럼, 람다식에 의해 계산되는 값을 요소로 하는 무한 스트림을 생성해서 반환하지만, iterate()와 달리, 이전 결과를 이용해서 다음 요소를 계산하지 않는다.

```java
Stream<Double> randomStream = Stream.generate(Math::random);
Stream<Integer> oneStream = Stream.generate(() -> 1);
```

그리고 generate()에 정의된 매개변수의 타입은 Supplier\<T\>이므로 매개변수가 없는 람다식만 허용된다. 한 가지 주의할 점은 iterate()와 generate()에 의해 생성된 스트림은 아래와 같이 기본형 스트림 타입의 참조변수로 다룰 수 없다는 것이다.

```java
IntStream evenStream = Stream.iterate(0, n -> n + 2);           // ERROR
DoubleStream randomStream = Stream.generate(Math::random);      // ERROR
```

굳이 필요하다면, 아래와 같이 mapToInt()와 같은 메서드로 변환을 해야 한다.

```java
IntStream evenStream = Stream.iterate(0, n -> n + 2).mapToInt(Integer::valueOf);
Stream<Integer> stream = evenStream.boxed();        // IntStream → Stream<Integer>
```


## 스트림 만들기 - 파일과 빈 스트림

java.nio.file.Files는 파일을 다루는 데 필요한 유용한 메서드들을 제공하는데, list()는 지정된 디렉토리(dir)에 있는 파일의 목록을 소스로 하는 스트림을 생성해서 반환한다.

(Path는 하나의 파일 또는 경로를 의미한다.)

```java
Stream<Path>        Files.list(Path dir)
```

이 외에도 Files클래스에는 Path를 요소로 하는 스트림을 생성하는 메서드가 더 있다.

그리고, 파일의 한 행(line)을 요소로 하는 스트림을 생성하는 메서드도 있다. 아래의 세 번째 메서드는 BufferedReader클래스에 속한 것인데, 파일 뿐만 아니라 다른 입력 대상으로부터도 데이터를 행 단위로 읽어올 수 있다.

```java
Stream<String> Files.lines(Path path)
Stream<String> Files.lines(Path path, Charset cs)
Stream<String> lines()      // BufferedReader클래스의 메서드
```


### 빈 스트림

요소가 하나도 없는 비어있는 스트림을 생성할 수도 있다. 스트림에 연산을 수행한 결과가 하나도 없을 때, null보다 빈 스트림을 반환하는 것이 낫다.

```java
Stream emptyStream = Stream.empty();    // empty()는 빈 스트림을 생성해서 반환한다.
long count = emptyStream.count();       // count의 값은 0
```

count()는 스트림 요소의 개수를 반환하며, 위의 문장에서 변수 count의 값은 0이 된다.