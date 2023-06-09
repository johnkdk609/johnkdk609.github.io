---
layout: post
title: 조건 연산자 (ternary operator)
categories: Java
date: 2023-03-31 23:47:00 +09:00
description: Java의 정석 기초편 ch. 3 연산자
---
조건 연산자는 조건식, 식1, 식2 모두 세 개의 피연산자를 필요로 하는 삼항 연산자이며, 삼항 연산자는 조건 연산자 하나뿐이다.

조건 연산자는 첫 번째 피연산자인 조건식의 평가결과에 따라 다른 결과를 반환한다. 조건식의 평가결과가 true이면 식1이, false이면 식2가 연산결과가 된다. 가독성을 높이기 위해 조건식을 괄호()로 둘러싸는 경우가 많지만 필수는 아니다.

```java
result = (x > y) ? x : y; // 괄호 생략 가능
```

위의 문장에서 식 'x > y'의 결과가 true이면, 변수 result에는 x의 값이 저장되고, false이면 y의 값이 저장된다.


만일 x의 값이 5, y의 값이 3이라면, 이 식은 다음과 같은 과정으로 계산된다.


```
result = (x > y) ? x : y;
-> result = (5 > 3) ? 5 : 3;
-> result = (true) ? 5 : 3;  // 조건식이 true(참)이므로 연산결과는 5
-> result = 5;
```


조건 연산자는 조건문인 if문으로 바꿔 쓸 수 있으며, if문 대신 조건 연산자를 사용하면 코드를 보다 간단히 할 수 있다.

```java
result = (x > y) ? x : y;
```

위의 조건 연산자를 if문으로 바꾸면 다음과 같다.

```
if (x > y)
  result = x; // x > y가 true일 때
else
  result = y; // x > y가 false일 때
```


그리고, 조건 연산자의 식1과 식2, 이 두 피연산자의 타입이 다른 경우, 이항 연산자처럼 산술 변환이 발생한다.


```
x = x + (mod < 0.5 ? 0 : 0.5) // 0과 0.5의 타입이 다르다.

-> x = x + (mod < 0.5 ? 0.0 : 0.5)  // 0이 0.0으로 자동 형변환되었다.
```

위의 식에서 조건 연산자의 피연산자 0과 0.5의 타입이 다르므로, 자동 형변환이 일어나서 double 타입으로 통일되고 연산 결과 역시 double 타입이 된다.
