---
layout: post
title: 람다식(Lambda Expression)
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-12 12:30:00 +0900
---
람다식(Lambda Expression)은 간단히 말해서 메서드를 하나의 '식(expression)'으로 표현한 것이다. 람다식은 함수를 간략하면서도 명확하게 표현할 수 있게 해준다.

메서드를 람다식으로 표현하면 메서드의 이름과 반환값이 없어지므로, 람다식을 '익명 함수(anonymous function)'라고도 한다.

```java
int[] arr = new int[5];
Arrays.setAll(arr, (i) -> (int)(Math.random() * 5) + 1);
```

위 문장에서 '(i) -> (int)(Math.random() * 5) + 1'이 바로 람다식이다. 이 람다식이 하는 일을 메서드로 표현하면 다음과 같다.

```java
int method(int i) {
    return (int)(Math.random() * 5) + 1;
}
```

위의 메서드보다 람다식이 간결하면서도 이해하기 쉽다는 것에 이견이 없을 것이다. 게다가 모든 메서드는 클래스에 포함되어야 하므로 클래스도 새로 만들어야 하고, 객체도 생성해야만 비로소 이 메서드를 호출할 수 있다. 그러나 **람다식은 이 모든 과정 없이 오직 람다식 자체만으로도 이 메서드의 역할을 대신할 수 있다.**

게다가 람다식은 메서드의 매개변수로 전달되어지는 것이 가능하고, 메서드의 결과로 반환될 수도 있다. 람다식으로 인해 메서드를 변수처럼 다루는 것이 가능해진 것이다.


## 람다식 작성하기

메서드를 람다식으로 만드는 방법은 아주 간단하다. 메서드에서 이름과 반환타입을 제거하고 매개변수 선언부와 몸통{} 사이에 '->'를 추가하기만 하면 된다.

```java
int max(int a, int b) {
    return a > b ? a : b;
}
```

↓

```java
(int a, int b) -> {
    return a > b ? a : b;
}
```

반환값이 있는 메서드의 경우, return문 대신 '식(expression)'으로 대신할 수 있다. 식의 연산결과가 자동적으로 반환값이 된다. 이때는 '문장(statement)'이 아닌 '식'이므로 끝에 ';'을 붙이지 않는다.

```java
(int a, int b) -> { return a > b ? a : b; }
```

↓

```java
(int a, int b) -> a > b ? a : b
```

람다식에 선언된 매개변수의 타입은 추론이 가능한 경우는 생략할 수 있는데, 대부분의 경우에 생략가능하다. 람다식에 반환타입이 없는 이유도 항상 추론이 가능하기 때문이다.

```java
(int a, int b) -> a > b ? a : b
```

↓

```java
(a, b) -> a > b ? a : b;
```

아래와 같이 선언된 매개변수가 하나뿐인 경우에는 괄호()를 생략할 수 있다. 단, 매개변수의 타입이 있으면 괄호()를 생략할 수 없다.

```java
(a) -> a * a
(int a) -> a * a
```

↓

```java
a -> a * a      // OK
int a -> a * a  // ERROR
```

마찬가지로 괄호{} 안의 문장이 하나일 때는 괄호{}를 생략할 수 있다. 이때 문장의 끝에 ';'을 붙이지 않아야 한다는 것에 주의해야 한다.

```java
(String name, int i) -> {
    System.out.println(name + "=" + i);
}
```

↓

```java
(String name, int i) ->
    System.out.println(name + "=" + i)
```

그러나 괄호{} 안의 문장이 return문일 경우 괄호{}를 생략할 수 없다.

```java
(int a, int b) -> { return a > b ? a : b; }     // OK
(int a, int b) -> return a > b ? a : b          // ERROR
```


## 람다식의 예

아래의 표는 메서드를 람다식으로 변환하여 보여준다.

<table>
    <tr>
        <th>메서드</th>
        <th>람다식</th>
    </tr>
    <tr>
        <td rowspan="3">
<pre>
<code>
int max(int a, int b) {
    return a > b ? a : b;
}
</code>
</pre>
        </td>
        <td>
<pre>
<code>
(int a, int b) -> { return a > b ? a : b; }
</code>
</pre>
        </td>
    </tr>
    <tr>
        <td>
<pre>
<code>
(int a, int b) -> a > b ? a : b
</code>
</pre>
        </td>
    </tr>
    <tr>
        <td>
<pre>
<code>
(a, b) -> a > b ? a : b;
</code>
</pre>
        </td>
    </tr>
    <tr>
        <td rowspan="3">
<pre>
<code>
void printVar(String name, int i) {
    System.out.println(name + "=" + i);
}
</code>
</pre>
        </td>
        <td>
<pre>
<code>
(string name, int i) ->
    { System.out.println(name + "=" + i); }
</code>
</pre>
        </td>
    </tr>
    <tr>
        <td>
<pre>
<code>
(name, i) ->
    { System.out.println(name + "=" + i); }
</code>
</pre>
        </td>
    </tr>
    <tr>
        <td>
<pre>
<code>
(name, i) ->
    System.out.println(name + "=" + i)
</code>
</pre>
        </td>
    </tr>
    <tr>
        <td rowspan="3">
<pre>
<code>
int square(int x) {
    return x * x;
}
</code>
</pre>
        </td>
        <td>
<pre>
<code>
(int x) -> x * x
</code>
</pre>
        </td>
    </tr>
    <tr>
        <td>
<pre>
<code>
(x) -> x * x
</code>
</pre>
        </td>
    </tr>
    <tr>
        <td>
<pre>
<code>
x -> x * x
</code>
</pre>
        </td>
    </tr>
    <tr>
        <td rowspan="2">
<pre>
<code>
int roll() {
    return (int)(Math.random() * 6);
}
</code>
</pre>
        </td>
        <td>
<pre>
<code>
() -> { return (int)(Math.random() * 6); }
</code>
</pre>
        </td>
    </tr>
    <tr>
        <td>
<pre>
<code>
() -> (int)(Math.random() * 6)
</code>
</pre>
        </td>
    </tr>
    <tr>
        <td>
<pre>
<code>
int sumArr(int[] arr) {
    int sum = 0;
    for (int i : arr)
        sum += i;
    return sum;
}
</code>
</pre>
        </td>
        <td>
<pre>
<code>
(int[] arr) -> {
    int sum = 0;
    for (int i : arr)
        sum += i;
    return sum;
}
</code>
</pre>
        </td>
    </tr>
</table>


## 람다식은 익명 함수? 익명 객체!

자바에서 모든 메서드는 클래스 내에 포함되어야 하는데, 람다식은 어떤 클래스에 포함되는 것일까? 지금까지 람다식이 메서드와 동등한 것처럼 설명해왔지만, 사실 람다식은 익명 클래스의 객체와 동등하다.

```java
(int a, int b) -> a > b ? a : b;
```

↕︎

```java
new Object() {
    int max(int a, int b) {
        return a > b ? a : b;
    }
}
```

위의 아래쪽 코드에서 메서드 이름 max는 임의로 붙인 것일 뿐 의미는 없다. 어쨌든 람다식으로 정의된 익명 객체의 메서드를 어떻게 호출할 수 있을 것인가? 이미 알고 있는 것처럼 참조변수가 있어야 객체의 메서드를 호출할 수 있으니까 일단 이 익명 객체의 주소를 f라는 참조변수에 저장해 보겠다.

```java
타입 f = (int a, int b) -> a > b ? a : b;   // 참조변수의 타입을 뭘로 해야 할까?
```

그러면, 참조변수 f의 타입은 어떤 것이어야 할까? 참조형이니까 클래스 또는 인터페이스가 가능하다. 그리고 람다식과 동등한 메서드가 정의되어 있는 것이어야 한다. 그래야 참조변수로 익명 객체(람다식)의 메서드를 호출할 수 있기 때문이다.