---
layout: post
title: Short Circuit Evaluation
categories: Java
date: 2024-01-15 23:33:00 +0900
---
자바의 논리 연산자에는 '&&'와 '&#124;&#124;'가 있다.

* && - 피연산자 모두가 true일 경우에만 true
* &#124;&#124; - 피연산자 중 하나라도 true일 경우 true

<br>

이때, 자바에는 <b>"Short Circuit Evaluation"</b>이라는 것이 작동한다.

* && 연산일 경우 - 만약 앞에 있는 것이 false라면, 뒤에 것은 볼 것도 없이 전체 결과가 false가 되는 것이다. 즉, 뒤는 고려하지 않고 바로 결과를 도출한다.
* &#124;&#124; 연산일 경우 - 앞이 true라면, 어차피 전체 결과가 true이다. 그러므로 뒤는 평가하지 않고 바로 결과를 도출한다.

<br>

만약 &, &#124;가 한 번씩만 있으면, Short Circuit Evaluation이 적용되지 않는다.

<br>

다음 코드를 보면 이해가 잘 될 것이다.

```java
int a = 15;
int b = 10;

System.out.println((a += 10) > 20 | (b -= 5) > 10);
System.out.println(a);
System.out.println(b);
```

위 코드의 출력 결과는 어떻게 될까?

```
true
25
5
```

우선, 논리 연산자가 '&#124;'이니, ```(a += 10) > 20```이 true면 ```(b -= 5) > 10```가 false인 것과 상관없이 전체 결과는 true이다. 그리고, '&#124;'가 한 번만 쓰였으므로 a는 25, b는 5가 된다.

<br>

반면 다음 코드를 보자.

```java
int a = 15;
int b = 10;

System.out.println((a += 10) > 20 || (b -= 5) > 10);
System.out.println(a);
System.out.println(b);
```

위 코드의 출력 결과는 어떻게 될까?

```
true
25
10
```

<br>

이전 코드와 다른 점은 논리 연산자가 수직선('&#124;') 1개에서 2개로 바뀌었다는 것이다. 그런데 b의 값이 10으로, ```b -= 5```가 적용되지 않은 것을 알 수 있다.

이는 수직선이 2개 사용되어서, ```(a += 10) > 20```이 true이므로 Short Circuit Evaluation이 적용되지 않아 ```(b -= 5) > 10```이 판단되지 않고 종료된 것이다. 즉, ```b -= 5```가 아예 실행되지 않은 것이다.