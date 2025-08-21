---
layout: post
title: Programmers - 가장 큰 수
categories: Algorithm
description: Java의 compareTo 메서드
date: 2025-08-21 21:50:00 +0900
---
<a href="https://school.programmers.co.kr/learn/courses/30/lessons/42746" target="_blank">Programmers - 가장 큰 수</a> 문제의 경우 정렬 유형의 문제인데, 아이디어가 필요하다. 일반적인 오름차순 정렬이나 내림차순 정렬을 해서는 안 된다.

핵심 아이디어는 다음과 같다. 배열 안의 값들을 일단 전부 문자열(String) 타입으로 변환해둔 상황에서, 두 수를 골라 합할 때에는

```a + b``` 와 ```b + a``` 의 두 가지 경우가 있다.

그러면 이 두 가지 경우 중 더 큰 값이 되는 경우를 앞에 두도록, 오름차순 정렬을 하는 것이다.

가령, 30과 3 이라는 두 개의 숫자가 있을 때, 두 숫자를 문자열로 변환한 다음 합친 경우는 "303"과 "330" 두 가지가 있다. 이 중에 "330"의 숫자가 "303"보다 크니, 3을 30 보다 앞에 위치시켜야 하는 것이다.

<br>

Java에서는 이때 ```compareTo``` 메서드를 사용한다.

<br>

우선 답안 코드는 다음과 같다.

```java
import java.util.*;

class Solution {
    public String solution(int[] numbers) {
        String answer = "";
        
        String[] arr = new String[numbers.length];
        for (int i = 0; i < numbers.length; i++) {
            arr[i] = String.valueOf(numbers[i]);
        }
        
        Arrays.sort(arr, (a, b) -> {
            return (b + a).compareTo(a + b);
        });
        
        if (arr[0].equals("0")) {   // edge case - numbers 배열 안의 숫자가 전부 0인 경우
            answer = "0";
        } else {
            for (String str : arr) {
                answer += str;
            }   
        }
        
        return answer;
    }
}
```

<br>

## Java의 compareTo 메서드

```compareTo``` 메서드는 Java에서 정렬/비교 시 핵심적으로 쓰이는 메서드이다.

### 1. 정의

* ```int compareTo(T o)```
* ```Comparable<T>``` 인터페이스에 정의된 메서드
* 자기 자신(this)과 매개변수(o)를 비교해서 순서를 결정

### 2. 반환값 규칙

* 음수 (< 0) → this < o
* 0 → this == o
* 양수 (> 0) → this > o

즉, 결과가 음수면 그대로 두고, 양수면 위치를 바꿔서 오름차순 정렬이 된다.

### 3. 기본 예시

```java
String a = "apple";
String b = "banana";

System.out.println(a.compareTo(b));     // 음수 (a < b)
System.out.println(b.compareTo(a));     // 양수 (b > a)
System.out.println(a.compareTo("apple"));   // 0
```

### 4. 특징 & 주의점

* 문자열은 사전순(lexicographical)으로 비교
* 숫자 래퍼 클래스(```Integer```, ```Double``` 등)도 ```compareTo``` 구현되어 있음
* ```compareTo```는 오름차순 기준 → 내림차순은 보통 비교 순서를 바꾸거나 -1 을 곱해서 사용
* ```equals()``` 와 일관성 유지해야 함 (같으면 0 반환)

<br>

## 추가 설명

```(a + b).compareTo(b + a)``` 와 ```(b + a).compareTo(a + b)``` 는 반대 방향의 정렬 기준을 만들어낸다.

### 1. (a + b).compareTo(b + a)

* ```Arrays.sort```나 ```Collections.sort```에서 이것을 쓰면 → 오름차순 기준이 된다.
* 즉, ```(a + b)``` 문자열이 ```(b + a)```보다 작으면 a가 앞에 오도록 정렬해버린다.
* 결과적으로 우리가 원하는 "가장 큰 수" 문제의 정답과는 반대로, 작은 수가 앞으로 모이는 결과가 된다.

예시를 보면 다음과 같다. a = 3, b = 30 인 경우에,

* ```(a + b) = "330"```, ```(b + a) = "303"```
* ```"330".compareTo("303")``` → 양수 → ```a(3)```이 뒤로 가버린다.
* 따라서 ```[30, 3]``` 의 순서가 된다.

### 2. (b + a).compareTo(a + b)

* 비교 순서를 반대로 둔 것이라서 사실상 내림차순 정렬 기준이 된다.
* ```(b + a)```가 ```(a + b)```보다 크면 b를 앞에 두고, 아니면 a를 앞에 둔다.
* 그래서 더 큰 수가 앞으로 오게 되고, "가장 큰 수" 문제를 풀 수 있는 정렬 기준이 되는 것이다.

예시를 보자. a = 3, b = 30 인 경우에,

* ```(a + b) = "330"```, ```(b + a) = "303"```
* ```"303".compareTo("330")``` → 음수 → ```b(30)```는 뒤로 밀리고 ```a(3)```이 앞으로 온다.
* 따라서 ```[3, 30]``` 의 순서로, 내가 원하는 결과가 나온다.