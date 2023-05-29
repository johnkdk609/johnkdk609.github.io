---
layout: post
title: Arrays로 배열 다루기
description: Java의 정석 기초편 ch. 5 배열
categories: Java
date: 2023-05-29 12:09:00 +09:00
---
Arrays클래스는 배열을 다루는 데에 유용한 메서드를 제공하는데, 그중에서도 자주 사용되는 것들 몇 가지만 먼저 알아보겠다.

## 배열의 비교와 출력 - equals(), toString()

toString() 메서드는 배열의 모든 요소를 문자열로 편하게 출력할 수 있다. toString()은 일차원 배열에만 사용할 수 있으므로, 다차원 배열에는 deepToString()을 사용해야 한다. deepToString()은 배열의 모든 요소를 재귀적으로 접근해서 문자열을 구성하므로 2차원뿐만 아니라 3차원 이상의 배열에도 동작한다. 

```java
int[] arr = {0, 1, 2, 3, 4};
int[][] arr2D = { {11, 12}, {21, 22} };

System.out.println(Arrays.toString(arr));         // [0, 1, 2, 3, 4]
System.out.println(Arrays.deepToString(arr2D));   // [[11, 12], [21, 22]]
```

equals() 메서드는 두 배열에 저장된 모든 요소를 비교해서 같으면 true, 다르면 false를 반환한다. equals()도 일차원 배열에만 사용 가능하므로, 다차원 배열의 비교에는 deepEquals()를 사용해야 한다.

```java
String[][] str2D = new String[][]{ {"aaa", "bbb"}, {"AAA", "BBB"} };
String[][] str2D2 = new String[][]{ {"aaa", "bbb"}, {"AAA", "BBB"} };

System.out.println(Arrays.equals(str2D, str2D2));       // false
System.out.println(Arrays.deepEquals(str2D, str2D2));   // true
```


## 배열의 복사 - copyOf(), copyOfRange()

copyOf()는 배열 전체를, copyOfRange()는 배열의 일부를 복사해서 새로운 배열을 만들어 반환한다. 늘 그렇듯이 copyOfRange()에 지정된 범위의 끝은 포함되지 않는다.

```java
int[] arr = {0, 1, 2, 3, 4};
int[] arr2 = Arrays.copyOf(arr, arr.length);    // arr2 = [0, 1, 2, 3, 4]
int[] arr3 = Arrays.copyOf(arr, 3);             // arr3 = [0, 1, 2]
int[] arr4 = Arrays.copyOf(arr, 7);             // arr4 = [0, 1, 2, 3, 4, 0, 0]
int[] arr5 = Arrays.copyOfRange(arr, 2, 4);     // arr5 = [2, 3]  ← 4는 불포함
int[] arr6 = Arrays.copyOfRange(arr, 0, 7);     // arr6 = [0, 1, 2, 3, 4, 0, 0]
```


## 배열의 정렬 - sort()

배열을 정렬할 때는 sort()를 사용한다.

```java
int[] arr = { 3, 2, 0, 1, 4 };
Arrays.sort(arr);   // 배열 arr를 정렬한다.
System.out.println(Arrays.toString(arr));   // [0, 1, 2, 3, 4]
```
