---
layout: post
title: Arrays클래스의 메서드
description: Java의 정석 기초편 ch. 11 컬렉션 프레임웍
categories: Java
date: 2023-05-20 22:40:00 +09:00
---
Arrays클래스에는 배열을 다루는 데에 유용한 메서드가 정의되어 있다. 같은 기능의 메서드가 배열의 타입만 다르게 오버로딩되어 있어서 많아 보이지만, 실제로는 그리 많지 않다. 아래는 Arrays에 정의된 toString()인데, 모든 기본형 배열과 참조형 배열별로 하나씩 정의되어 있다.

(Arrays에 정의된 메서드는 모두 static메서드이다.)

```java
static String toString(boolean[] a)
static String toString(byte[] a)
static String toString(char[] a)
static String toString(short[] a)
static String toString(int[] a)
static String toString(long[] a)
static String toString(float[] a)
static String toString(double[] a)
static String toString(Object[] a)
```

같은 메서드를 배열의 타입별로 일일이 설명할 필요는 없으므로, 앞으로 매개변수의 타입이 int배열인 메서드에 대한 사용법만 알아볼 것이다.


### 배열의 복사 - copyOf(), copyOfRange()

copyOf()는 배열 전체를, copyOfRange()는 배열의 일부를 복사해서 새로운 배열을 만들어 반환한다. 늘 그렇듯이 copyOfRange()에 지정된 범위의 끝은 포함되지 않는다.

```java
int[] arr = {0, 1, 2, 3, 4};
int[] arr2 = Arrays.copyOf(arr, arr.length);  // arr2 = [0, 1, 2, 3, 4]
int[] arr3 = Arrays.copyOf(arr, 3);           // arr3 = [0, 1, 2]
int[] arr4 = Arrays.copyOf(arr, 7);           // arr4 = [0, 1, 2, 3, 4, 0, 0]
int[] arr5 = Arrays.copyOfRange(arr, 2, 4);   // arr5 = [2, 3]  <- 4는 불포함
int[] arr6 = Arrays.copyOfRange(arr, 0, 7);   // arr6 = [0, 1, 2, 3, 4, 0, 0]
```

### 배열 채우기 - fill(), setAll()

fill()은 배열의 모든 요소를 지정된 값으로 채운다. setAll()은 배열을 채우는 데 사용할 함수형 인터페이스를 매개변수로 받는다. 이 메서드를 호출할 때는 함수형 인터페이스를 구현한 객체를 매개변수로 지정하든가 아니면 람다식을 지정해야 한다.

```java
int[] arr = new int[5];
Arrays.fill(arr, 9);    // arr = [9, 9, 9, 9, 9]
Arrays.setAll(arr, (i) -> (int)(Math.random()*5)+1);    // arr = [1, 5, 2, 1, 1]
```

위의 문장에 사용된 '(i) -> (int)(Math.random()\*5)+1)'은 '람다식(lambda expression)'인데, 1~5의 범위에 속한 임의의 정수를 반환하는 일을 한다. 그리고 setAll()메서드는 이 람다식이 반환한 임의의 정수로 배열을 채운다.


### 배열의 정렬과 검색 - sort(), binarySearch()

sort()는 배열을 정렬할 때, 그리고 배열에 저장된 요소를 검색할 때는 binarySearch()를 사용한다. binarySearch()는 배열에서 지정된 값이 저장된 위치(index)를 찾아서 반환하는데, **반드시 배열이 정렬된 상태이어야 올바른 결과를 얻는다.** 그리고 만일 검색한 값과 일치하는 요소들이 여러 개 있다면, 이 중에서 어떤 것의 위치가 반환될지는 알 수 없다. 

```java
int[] arr = {3, 2, 0, 1, 4};
int idx = Arrays.binarySearch(arr, 2);    // idx = -5 <- 잘못된 결과

Arrays.sort(arr);
System.out.println(Arrays.toString(arr)); // [0, 1, 2, 3, 4]
int idx = Arrays.binarySearch(arr, 2);    // idx = 2 <- 올바른 결과
```

배열의 첫 번째 요소부터 순서대로 하나씩 검색하는 것을 '순차 검색(linear search)'이라고 하는데, 이 검색 방법은 배열이 정렬되어 있을 필요는 없지만 배열의 요소를 하나씩 비교하기 때분에 시간이 많이 걸린다. 반면에 이진 검색(binary search)은 배열의 검색할 범위를 반복적으로 절반씩 줄여가면서 검색하기 때문에 검색 속도가 상당히 빠르다. 배열의 길이가 10배가 늘어나도 검색 횟수는 3~4회밖에 늘어나지 않으므로 큰 배열의 검색에 유리하다. 단, 배열이 정렬이 되어 있는 경우에만 사용할 수 있다는 단점이 있다. 


### 문자열의 비교와 출력 - equals(), toString()

toString()으로 배열의 모든 요소를 문자열로 편하게 출력할 수 있다. toString()은 일차원 배열에만 사용할 수 있으므로, 다차원 배열에는 deepToString()을 사용해야 한다. deepToString()은 배열의 모든 요소를 재귀적으로 접근해서 문자열을 구성하므로 2차원뿐만 아니라 3차원 이상의 배열에도 동작한다. 

```java
int[] arr = {0, 1, 2, 3, 4};
int[][] arr2D = { {11, 12}, {21, 22} };

System.out.println(Arrays.toString(arr));         // [0, 1, 2, 3, 4]
System.out.println(Arrays.deepToString(arr2D));   // [[11, 12], [21, 22]]
```

equals()는 두 배열에 저장된 모든 요소를 비교해서 같으면 true, 다르면 false를 반환한다. equals()도 일차원 배열에만 사용 가능하므로, 다차원 배열의 비교에는 deepEquals()를 사용해야 한다.

```java
String[][] str2D = new String[][]{ {"aaa", "bbb"}, {"AAA", "BBB"} };
String[][] str2D2 = new String[][]{ {"aaa", "bbb"}, {"AAA", "BBB"} };

System.out.println(Arrays.equals(str2D, str2D2));       // false
System.out.println(Arrays.deepEquals(str2D, str2D2));   // true
```

위와 같이 2차원 String배열을 equals()로 비교하면 배열에 저장된 내용이 같은데도 false를 결과로 얻는다. **다차원 배열은 '배열의 배열'의 형태로 구성하기 때문에 equals()로 비교하면, 문자열을 비교하는 것이 아니라 '배열에 저장된 배열의 주소'를 비교하게 된다.** 서로 다른 배열은 항상 주소가 다르므로 false를 결과로 얻는다. 


### 배열을 List로 변환 - asList(Object... a)

asList()는 배열을 List에 담아서 반환한다. 매개변수의 타입이 가변인수라서 배열 생성 없이 저장할 요소들만 나열하는 것도 가능하다.

```java
List list = Arrays.asList(new Integer[]{1, 2, 3, 4, 5});    // list = [1, 2, 3, 4, 5]
List list = Arrays.asList(1, 2, 3, 4, 5);                   // list = [1, 2, 3, 4, 5]
list.add(6);    // UnsupportedOperationException 예외 발생
```

**한 가지 주의할 점은 asList()가 반환한 List의 크기를 변경할 수 없다는 것이다. 즉, 추가 또는 삭제가 불가능하다. 저장된 내용은 변경 가능하다.** 만일 크기를 변경할 수 있는 List가 필요하다면 다음과 같이 하면 된다.

```java
List list = new ArrayList(Arrays.asList(1, 2, 3, 4, 5));
```


### parallelXXX(), spliterator(), stream()

이 외에도 'parallel'로 시작하는 이름의 메서드들이 있는데, 이 메서드들은 보다 빠른 결과를 얻기 위해 여러 쓰레드가 작업을 나눠서 처리하도록 한다. spliterator()는 여러 쓰레드가 처리할 수 있게 하나의 작업을 여러 작업으로 나누는 Spliterator를 반환하며, stream()은 컬렉션을 스트림을 변환한다.


## Arrays의 메서드 예제

```java
import java.util.*;

class Ex11_6 {
	public static void main(String[] args) {
		int[]	   arr   = {0,1,2,3,4};
		int[][]	arr2D = { {11,12,13}, {21,22,23} };

		System.out.println("arr="+Arrays.toString(arr));
		System.out.println("arr2D="+Arrays.deepToString(arr2D));

		int[] arr2 = Arrays.copyOf(arr, arr.length);
		int[] arr3 = Arrays.copyOf(arr, 3);          
		int[] arr4 = Arrays.copyOf(arr, 7);          
		int[] arr5 = Arrays.copyOfRange(arr, 2, 4);  
		int[] arr6 = Arrays.copyOfRange(arr, 0, 7);  

		System.out.println("arr2="+Arrays.toString(arr2));
		System.out.println("arr3="+Arrays.toString(arr3));
		System.out.println("arr4="+Arrays.toString(arr4));
		System.out.println("arr5="+Arrays.toString(arr5));
		System.out.println("arr6="+Arrays.toString(arr6));
		
		int[] arr7 =  new int[5];
		Arrays.fill(arr7, 9);  // arr=[9,9,9,9,9]
		System.out.println("arr7="+Arrays.toString(arr7));

		Arrays.setAll(arr7, i -> (int)(Math.random()*6)+1);
		System.out.println("arr7="+Arrays.toString(arr7));

		for(int i : arr7) {
			char[] graph = new char[i];
			Arrays.fill(graph, '*');
			System.out.println(new String(graph)+i);
		}

		String[][] str2D  = new String[][]{ {"aaa","bbb"}, {"AAA","BBB"} };
		String[][] str2D2 = new String[][]{ {"aaa","bbb"}, {"AAA","BBB"} };

		System.out.println(Arrays.equals(str2D, str2D2));     // false
		System.out.println(Arrays.deepEquals(str2D, str2D2)); // true

		char[] chArr = { 'A', 'D', 'C', 'B', 'E' };

		System.out.println("chArr="+Arrays.toString(chArr));
		System.out.println("index of B ="+Arrays.binarySearch(chArr, 'B'));
		System.out.println("= After sorting =");
		Arrays.sort(chArr);
		System.out.println("chArr="+Arrays.toString(chArr));
		System.out.println("index of B ="+Arrays.binarySearch(chArr, 'B'));
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
arr=[0, 1, 2, 3, 4]
arr2D=[[11, 12, 13], [21, 22, 23]]
arr2=[0, 1, 2, 3, 4]
arr3=[0, 1, 2]
arr4=[0, 1, 2, 3, 4, 0, 0]
arr5=[2, 3]
arr6=[0, 1, 2, 3, 4, 0, 0]
arr7=[9, 9, 9, 9, 9]
arr7=[4, 2, 3, 2, 1]
****4
**2
***3
**2
*1
false
true
chArr=[A, D, C, B, E]
index of B =-2				← 정렬하지 않아서 잘못된 결과가 나왔음.
= After sorting =
chArr=[A, B, C, D, E]
index of B =1				← 정렬한 후라서 올바른 결과가 나왔음.
```
