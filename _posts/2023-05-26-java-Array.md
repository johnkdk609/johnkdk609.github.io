---
layout: post
title: 배열 (Array)
description: Java의 정석 기초편 ch. 5 배열
categories: Java
date: 2023-05-26 15:26:00 +09:00
---
같은 타입의 여러 변수를 하나의 묶음으로 다루는 것을 '배열(array)'이라고 한다. 많은 양의 데이터를 저장하기 위해서, 그 데이터의 숫자만큼 변수를 선언해야 한다면 매우 혼란스러울 것이다. 10,000개의 데이터를 저장하기 위해 10,000개의 변수를 선언해야 한다면 상상하는 것만으로도 상당히 곤혹스럽다. 

이런 경우에 배열을 사용하면 많은 양의 데이터를 손쉽게 다룰 수 있다.

> **"배열은 같은 타입의 여러 변수를 하나의 묶음으로 다루는 것"**

여기서 중요한 것은 '같은 타입'이어야 한다는 것이며, 서로 다른 타입의 변수들로 구성된 배열은 만들 수 없다. 한 학급의 시험점수를 저장하고자 할 때가 배열을 사용하기 좋은 예이다.

만일 배열을 사용하지 않는다면 학생 5명의 점수를 저장하기 위해서 아래와 같이 5개의 변수를 선언해야 할 것이다.

```java
int score1, score2, score3, score4, score5 ;
```
<img width="550" alt="[자바의 정석 - 기초편] ch5-1~3 배열의 생성과 선언, 배열의 인덱스" title="[자바의 정석 - 기초편] ch5-1~3 배열의 생성과 선언, 배열의 인덱스" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b9bfa2af-128c-4b44-924d-d69fea930d96">

변수 대신 배열을 이용하면 다음과 같이 간단히 처리할 수 있다. 변수의 선언과 달리 다뤄야 할 데이터의 수가 아무리 많아도 단지 배열의 길이만 바꾸면 된다.

```java
int[] score = new int[5];   // 5개의 int값을 저장할 수 있는 배열을 생성한다.
```

아래의 그림은 위의 코드가 실행되어 생성된 배열을 그림으로 나타낸 것이다. 값을 저장할 수 있는 공간은 score\[0\]부터 score\[4\]까지 모두 5개이며, 변수 score는 배열을 다루는 데 필요한 참조변수일 뿐 값을 저장하기 위한 공간은 아니다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ffd70a4d-5399-4b64-a525-49ed61c99f51" width="700" title="[자바의 정석 - 기초편] ch5-1~3 배열의 생성과 선언, 배열의 인덱스" alt="[자바의 정석 - 기초편] ch5-1~3 배열의 생성과 선언, 배열의 인덱스">

위의 그림에서 알 수 있듯이, 변수와 달리 배열은 각 저장공간이 연속적으로 배치되어 있다는 특징이 있다.


## 배열의 선언과 생성

배열을 선언하는 방법은 간단하다. 원하는 타입의 변수를 선언하고 변수 또는 타입에 배열임을 의미하는 대괄호\[\]를 붙이면 된다. 대괄호\[\]는 타입 뒤에 붙여도 되고 변수이름 뒤에 붙여도 된다.

(저자의 경우 대괄호를 타입에 붙이는 쪽을 선호한다고 한다. 대괄호가 변수이름의 일부라기보다는 타입의 일부라고 보기 때문이다.)

<table>
  <tr>
    <th>선언 방법</th>
    <th>선언 예</th>
  </tr>
  <tr>
    <td>타입[] 변수이름;</td>
    <td>int[] score;<br>String[] name;</td>
  </tr>
  <tr>
    <td>타입 변수이름[];</td>
    <td>int score[];<br>String name[];</td>
  </tr>
</table>


### 배열의 생성

배열을 선언한 다음에는 배열을 생성해야 한다. 배열을 선언하는 것은 단지 생성된 배열을 다루기 위한 참조변수를 위한 공간이 만들어질 뿐이고, 배열을 생성해야만 비로소 값을 저장할 수 있는 공간이 만들어지는 것이다. 배열을 생성하기 위해서는 연산자 'new'와 함께 배열의 타입과 길이를 지정해주어야 한다. 

```java
타입[] 변수이름;            // 배열을 선언(배열을 다루기 위한 참조변수 선언)
변수이름 = new 타입[길이];   // 배열을 생성(실제 저장공간을 생성)
```

아래의 코드는 '길이가 5인 int배열'을 생성한다.

```java
int[] score;            // int타입의 배열을 다루기 위한 참조변수 score 선언
score = new int[5];     // int타입의 값 5개를 저장할 수 있는 배열 생성
```

다음과 같이 배열의 선언과 생성을 동시에 하면 간략히 한 줄로 할 수 있는데, 대부분의 경우 이렇게 한다.

```java
타입[] 변수이름 = new 타입[길이];   // 배열의 선언과 생성을 동시에.
int[] score = new int[5];     // 길이가 5인 int배열
```
<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ffd70a4d-5399-4b64-a525-49ed61c99f51" width="700" title="[자바의 정석 - 기초편] ch5-1~3 배열의 생성과 선언, 배열의 인덱스" alt="[자바의 정석 - 기초편] ch5-1~3 배열의 생성과 선언, 배열의 인덱스">

(배열이 주소 0x100번지에 생성되었다고 가정한 그림이다.)


## 배열의 인덱스

생성된 배열의 각 저장공간을 '배열의 요소(element)'라고 하며, '배열이름\[인덱스\]'의 형식으로 배열의 요소에 접근한다. **인덱스(index)는 배열의 요소마다 붙여진 일련번호**로 각 요소를 구별하는 데에 사용된다. 인덱스는 1이 아닌 0부터 시작한다.

> **"인덱스(index)의 범위는 0부터 '배열 길이 - 1'까지."**

예를 들어 길이가 5인 배열은 모두 5개의 요소(저장공간)를 가지며 인덱스의 범위는 1부터 5까지가 아닌 0부터 4까지, 즉 0, 1, 2, 3, 4가 된다.

```java
int[] score = new int[5];
```

<img width="600" alt="[자바의 정석 - 기초편] ch5-1~3 배열의 생성과 선언, 배열의 인덱스" title="[자바의 정석 - 기초편] ch5-1~3 배열의 생성과 선언, 배열의 인덱스" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/08303a7e-130a-49d6-ab22-2ea5d0c499a5">

배열에 값을 저장하고 읽어오는 방법은 변수와 같다. 단지 변수이름 대신 '배열이름\[인덱스\]'를 사용한다는 점만 다르다.

```java
score[3] = 100;     // 배열 score의 4번째 요소에 100을 저장한다.
```

<img width="600" alt="[자바의 정석 - 기초편] ch5-1~3 배열의 생성과 선언, 배열의 인덱스" title="[자바의 정석 - 기초편] ch5-1~3 배열의 생성과 선언, 배열의 인덱스" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/06ebb5e5-7c1d-436d-81b4-1dcc09d1d851">

```java
int value = score[3];   // 배열 score의 4번째 요소의 값을 읽어서 value에 저장.
```

배열의 또 다른 장점은 index로 상수 대신 변수나 수식도 사용할 수 있다는 것이다. 그래서 아래 (보기 1) 코드를 (보기 2)와 같이 for문을 이용해서 간단히 할 수 있다. (보기 2) 코드는 index로 상수 대신 변수 i를 사용하고, for문으로 변수 i의 값을 0부터 3까지 증가시킨다. 

```java
// 보기 1

score[0] = 0;
score[1] = 10;
score[2] = 20;
score[3] = 30;
```

```java
// 보기 2

for (int i = 0; i <= 3; i++) {
  score[i] = i * 10;
}
```


## 배열의 길이(배열이름.length)

자바에서는 자바 가상 머신(JVM)이 모든 배열의 길이를 별도로 관리하며, '배열이름.length'를 통해서 배열의 길이에 대한 정보를 얻을 수 있다.

아래의 코드에서 배열 arr의 길이가 5이므로, arr.length의 값 역시 5가 된다.

```java
int[] arr = new int[5];   // 길이가 5인 int배열
int tmp = arr.length;     // arr.length의 값은 5이고 tmp에 5가 저장된다.
```

**배열은 한 번 생성하면 길이를 변경할 수 없기 때문에, 이미 생성된 배열의 길이는 변하지 않는다. 따라서 '배열이름.length'는 상수다. 즉, 값을 읽을 수만 있을 뿐 변경할 수는 없다.**

아래의 코드는 배열의 각 요소를 for문을 이용해서 출력한다. 여기서 배열 score의 길이는 6이며, 인덱스의 범위는 0~5이다.

```java
int[] score = new int[6];

for (int i = 0; i < 6; i++)
  System.out.println(score[i]);
```

이때 코드를 아래처럼 변경하여 배열의 길이를 줄이면, 인덱스의 유효범위는 0~4가 된다.

```java
int[] score = new int[5];     // 배열의 길이를 6에서 5로 변경

for (int i = 0; i < 6; i++)   // 실수로 조건식을 변경하지 않음
  System.out.println(score[i]);   // ERROR
```

배열의 길이가 변경되었으니 for문에 사용되는 조건의 범위도 변경해주어야 하는데, 만일 이것을 잊고 실행한다면 for문은 배열의 유효한 인덱스 범위인 0~4를 넘어 0부터 5까지 반복하기 때문에 5번째 반복에서 ArrayIndexOutOfBoundsException이라는 예외(배열의 index가 유효한 범위를 벗어났다는 에러)가 발생하여 비정상적으로 종료될 것이다. 

그래서 for문의 조건식에 배열의 길이를 직접 적어주는 것보다 '배열이름.length'를 사용하는 것이 좋다. 위의 for문을 '배열이름.length'를 사용해서 변경하면 다음과 같다.

```java
int[] score = new int[5];     // 배열의 길이를 6에서 5로 변경

for (int i = 0; i < score.length; i++)    // 조건식을 변경하지 않아도 됨 
  System.out.println(score[i]);
```

'배열이름.length'는 배열의 길이가 변경되면 자동적으로 변경된 배열의 길이를 값으로 갖기 때문에, 배열과 함께 사용되는 for문의 조건식을 일일이 변경해주지 않아도 된다.


## 배열의 초기화

배열은 생성과 동시에 자동적으로 기본값(0)으로 초기화되므로 배열을 사용하기 전에 따로 초기화를 해주지 않아도 되지만, 원하는 값을 저장하려면 아래와 같이 각 요소마다 값을 지정해줘야 한다.

```java
int[] score = new int[5];   // 길이가 5인 int형 배열을 생성한다.
score[0] = 50;              // 각 요소에 직접 값을 저장한다.
score[1] = 60;
score[2] = 70;
score[3] = 80;
score[4] = 90;
```

배열의 길이가 큰 경우에는 이렇게 요소 하나하나에 값을 지정하기 보다는 for문을 사용하는 것이 좋다. 위의 코드를 for문을 이용해서 바꾸면 다음과 같다.

```java
int[] score = new int[5];

for (int i = 0; i < score.length; i++)
  score[i] = i * 10 + 50;
```

그러나 for문으로 배열을 초기화하려면, 저장하려는 값에 일정한 규칙이 있어야만 가능하기 때문에 자바에서는 다음과 같이 배열을 간단히 초기화할 수 있는 방법을 제공한다.

```java
int[] score = new int[]{ 50, 60, 70, 80, 90 };    // 배열의 생성과 초기화를 동시에 
```

저장할 값들을 괄호{} 안에 쉼표로 구분해서 나열하면 되며, 괄호{} 안의 값의 개수에 의해 배열의 길이가 자동으로 결정되기 때문에 괄호\[\] 안에 배열의 길이는 안 적어도 된다.

```java
int[] score = new int[]{ 50, 60, 70, 80, 90 };
int[] score = { 50, 60, 70, 80, 90 };   // new int[]를 생략할 수 있음
```

심지어는 위와 같이 'new 타입\[\]'을 생략하여 코드를 더 간단히 할 수도 있다. 아무래도 생략된 형태의 코드가 더 간단하므로 자주 사용된다. 다만 다음과 같이 배열의 선언과 생성을 따로 하는 경우에는 생략할 수 없다는 점만 주의하면 된다.

```java
int[] score;
score = { 50, 60, 70, 80, 90 };             // ERROR. new int[]를 생략할 수 없음
score = new int[]{ 50, 60, 70, 80, 90 };    // OK
```


## 배열의 출력

배열을 초기화할 때 for문을 사용하듯이, 배열에 저장된 값을 확인할 때도 다음과 같이 for문을 사용하면 된다.

```java
int[] iArr = { 100, 95, 80, 70, 60 };

for (int i = 0; i < iArr.length; i++) {   // 배열의 요소를 순서대로 하나씩 출력
  System.out.println(iArr[i]);
}
```

println메서드는 출력 후에 줄 바꿈을 하므로, 여러 줄에 출력되어 보기 불편할 때가 있다. 그럴 때는 다음과 같이 출력 후에 줄 바꿈을 하지 않는 print메서드를 이용하면 된다.

```java
int[] iArr = { 100, 95, 80, 70, 60 };

for (int i = 0; i < iArr.length; i++) {
  System.out.println(iArr[i] + ",");    // 각 요소 간의 구별을 위해 쉼표를 넣는다.
}
System.out.println();   // 다음 출력이 바로 이어지지 않도록 줄 바꿈을 한다.
```

더 간단한 방법은 'Arrays.toString(배열이름)'메서드를 사용하는 것이다. 이 메서드는 배열의 모든 요소를 '\[첫번째 요소, 두번째 요소, ...\]'와 같은 형식의 문자열로 만들어서 반환한다.

(Arrays.toString()을 사용하려면, 'import java.util.Arrays;'를 추가해야 한다. 이클립스 단축키 ctrl+shift+o)

```java
int[] iArr = { 100, 95, 80, 70, 60 };
// 배열 iArr의 모든 요소를 출력한다. [100, 95, 80, 70, 60]이 출력된다.
System.out.println(Arrays.toString(iArr));
```

만일 iArr의 값을 바로 출력하면 어떻게 될까? '타입@주소'의 형식으로 출력된다. 배열을 가리키는 참조변수를 출력해봐야 별로 얻을 정보가 없다는 정도만 알아두면 된다.

```java
System.out.println(iArr);   // [I@14318bb와 같은 형식의 문자열이 출력된다.
```

예외적으로 char배열은 println메서드로 출력하면 각 요소가 구분자 없이 그대로 출력되는데, 이것은 println메서드가 char배열일 때만 이렇게 동작하도록 작성되었기 때문이다.

```java
char[] chArr = { 'a', 'b', 'c', 'd' };
System.out.println(chArr);    // abcd가 출력된다.
```

## 배열의 출력 예제

```java
import java.util.Arrays;  // Arrays.toString()을 사용하기 위해 추가 

class Ex5_1 {
	public static void main(String[] args) {
		int[] iArr1 = new int[10];
		int[] iArr2 = new int[10];
//		int[] iArr3 = new int[]{100, 95, 80, 70, 60};
		int[] iArr3 = {100, 95, 80, 70, 60};
		char[] chArr = {'a', 'b', 'c', 'd'};

		for (int i=0; i < iArr1.length; i++) {
			iArr1[i] = i + 1; // 1~10의 숫자를 순서대로 배열에 넣는다. 
		}

		for (int i=0; i < iArr2.length; i++) {
			iArr2[i] = (int)(Math.random()*10) + 1; // 1~10의 값을 배열에 저장 
		}

		// 배열에 저장된 값들을 출력한다. 
		for(int i=0; i < iArr1.length; i++) {
			System.out.print(iArr1[i]+",");	
		}
		System.out.println();

		System.out.println(Arrays.toString(iArr2));
		System.out.println(Arrays.toString(iArr3));
		System.out.println(Arrays.toString(chArr));
		System.out.println(iArr3);
		System.out.println(chArr);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
1,2,3,4,5,6,7,8,9,10,
[7, 4, 9, 3, 10, 4, 6, 4, 9, 8]
[100, 95, 80, 70, 60]
[a, b, c, d]
[I@724af044
abcd
```


## 배열의 활용(1) - 총합과 평균

```java
class Ex5_2 {
	public static void main(String[] args) {
		int   sum = 0;      // 총합을 저장하기 위한 변수 
		float average = 0f; // 평균을 저장하기 위한 변수 

		int[] score = {100, 88, 100, 100, 90};

		for (int i = 0; i < score.length; i++) {
			sum += score[i];
		}
		average = sum / (float)score.length ; // 계산 결과를 float타입으로 얻으려 형변환 

		System.out.println("총합 : " + sum);
		System.out.println("평균 : " + average);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
총합 : 478
평균 : 95.6
```

for문을 이용해서 배열에 저장된 값을 모두 더한 결과를 배열의 개수로 나누어서 평균을 구하는 예제이다. 평균을 구하기 위해서 전체 합을 배열의 길이인 score.length로 나누었다.

이때 int타입과 int타입 간의 연산은 int타입으로 결과를 얻기 때문에 정확한 평균값은 얻지 못하므로 score.length를 float타입으로 변환하여 나눗셈을 하였다.

```
478 / 5 → 95
478 / (float)5 → 478 / 5.0f → 478.0f / 5.0f → 95.6f
```


## 배열의 활용(2) - 최댓값과 최솟값

```java
class Ex5_3 { 
	public static void main(String[] args) { 
		int[] score = { 79, 88, 91, 33, 100, 55, 95 }; 

		int max = score[0]; // 배열의 첫 번째 값으로 최댓값을 초기화 한다. 
		int min = score[0]; // 배열의 첫 번째 값으로 최솟값을 초기화 한다. 

		for(int i=1; i < score.length;i++) {	// 배열의 두 번째 요소부터 읽기 위해서 변수 i의 값을 1로 초기화 했다. 
			if(score[i] > max) { 
				max = score[i]; 
			} else if(score[i] < min) { 
				min = score[i]; 
			} 
		} // end of for 

		System.out.println("최댓값 :" + max);       
		System.out.println("최솟값 :" + min);       
	} // end of main 
} // end of class
```

위 코드의 출력 결과는 다음과 같다.

```
최댓값 :100
최솟값 :33
```

배열에 저장된 값 중에서 최댓값과 최솟값을 구하는 예제이다. 배열의 첫 번째 요소 'score\[0\]'의 값으로 최댓값을 의미하는 변수 max와 최솟값을 의미하는 변수 min을 초기화 하였다.

그 다음 반복문을 통해서 배열의 두 번째 요소 'score\[1\]'부터 max와 비교하기 시작한다. 만일 배열에 담긴 값이 max에 저장된 값보다 크다면, 이 값을 max에 저장한다.

이런 식으로 배열의 마지막 요소까지 비교하고 나면 max에는 배열에 담긴 값 중에서 최댓값이 저장된다. 최솟값 min도 같은 방식으로 얻을 수 있다.


## 배열의 활용(3) - 섞기(shuffle)

```java
import java.util.Arrays;

class Ex5_4 {
	public static void main(String[] args) {
		int[] numArr = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
		System.out.println(Arrays.toString(numArr));

		for (int i=0; i < 100; i++ ) {
			int n = (int)(Math.random() * 10);  // 0~9 중의 한 값을 임의로 얻는다. 
			int tmp = numArr[0];	//
			numArr[0] = numArr[n];	//
			numArr[n] = tmp;	// numArr[0]과 numArr[n]의 값을 서로 바꾼다.
		}
		System.out.println(Arrays.toString(numArr));
	} // main의 끝 
}
```

위 코드의 출력 결과 예시는 다음과 같다.

```
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
[8, 2, 5, 9, 1, 0, 7, 6, 3, 4]
```

길이가 10인 배열 numArr을 생성하고 0~9의 숫자로 차례대로 초기화하여 출력한다. 그 다음 random()을 이용해서 배열의 임의의 위치에 있는 값과 배열의 첫 번째 요소 'numArr\[0\]'의 값을 교환하는 일을 100번 반복해서 배열의 요소가 뒤섞이게 한다.

만일 random()을 통해 얻은 값 n이 3이라면, 아래의 그림처럼 두 값이 서로 바뀐다.

```java
① tmp = numArr[0];	// numArr[0]의 값을 변수 tmp에 저장한다.
```

<img width="700" alt="[자바의 정석 - 기초편] ch5-10,11 배열의 활용(3)~(4)" title="[자바의 정석 - 기초편] ch5-10,11 배열의 활용(3)~(4)" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5b9eb589-d787-43e1-aec0-2f847e227296">

```java
② numArr[0] = numArr[3];	// numArr[3]의 값을 numArr[0]에 저장한다.
```

<img width="700" alt="[자바의 정석 - 기초편] ch5-10,11 배열의 활용(3)~(4)" title="[자바의 정석 - 기초편] ch5-10,11 배열의 활용(3)~(4)" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f2daf2de-4629-4fbe-8888-c68b4d48f70f">

```java
③ numArr[3] = tmp;	// tmp의 값을 numArr[3]에 저장한다.
```

<img width="700" alt="[자바의 정석 - 기초편] ch5-10,11 배열의 활용(3)~(4)" title="[자바의 정석 - 기초편] ch5-10,11 배열의 활용(3)~(4)" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b63e7cbe-2f74-43b0-bad2-522345f3bcd0">


## 배열의 활용(4) - 로또 번호 만들기

```java
class Ex5_5 { 
	public static void main(String[] args) { 
		int[] ball = new int[45];  // 45개의 정수값을 저장하기 위한 배열 생성      

		// 배열의 각 요소에 1~45의 값을 저장한다. 
		for(int i=0; i < ball.length; i++)       
			ball[i] = i + 1;    // ball[0]에 1이 저장된다. 

		int tmp = 0;   // 두 값을 바꾸는 데 사용할 임시변수 
		int j = 0;     // 임의의 값을 얻어서 저장할 변수 

		// 배열의 i번째 요소와 임의의 요소에 저장된 값을 서로 바꿔서 값을 섞는다.  
		// 0번째 부터 5번째 요소까지 모두 6개만 바꾼다. 
		for(int i=0; i < 6; i++) {       
			j = (int)(Math.random() * 45); // 0~44 범위의 임의의 값을 섞는다. 
			tmp     = ball[i]; 
			ball[i] = ball[j]; 
			ball[j] = tmp; 
		} 

		// 배열 ball의 앞에서부터 6개의 요소를 출력한다. 
		for (int i=0; i < 6; i++) 
			System.out.printf("ball[%d]=%d%n", i, ball[i]); 
	} 
}
```

위 코드의 출력 결과 예시는 다음과 같다.

```
ball[0]=38
ball[1]=20
ball[2]=18
ball[3]=5
ball[4]=45
ball[5]=42
```

로또번호를 생성하는 예제이다. 길이가 45인 배열에 1부터 45까지의 값을 담은 다음 반복문을 이용해서 배열의 인덱스가 i인 값(ball\[i\])과 random()에 의해서 결정된 임의의 위치에 있는 값과 자리를 바꾸는 것을 6번 반복한다. 이것은 마치 1부터 45까지의 번호가 쓰인 카드를 잘 섞은 다음 맨 위의 6장을 꺼내는 것과 같다고 할 수 있다.

45개의 요소 중에서 앞에 6개의 요소만 임의의 위치에 있는 요소와 자리를 바꾸면 된다.

```java
// 배열의 인덱스가 i인 요소와 임의의 요소에 저장된 값을 서로 바꿔서 값을 섞는다.
// 0번째부터 5번째 요소까지 모두 6개만 바꾼다.
for (int i = 0; i < 6; i++) {
	j = (int)(Math.random() * 45);	// 0~44 범위의 임의의 값을 섞는다.
	tmp = ball[i];		//
	ball[i] = ball[j];	//
	ball[j] = tmp;		// ball[i]와 ball[j]의 값을 서로 바꾼다. 
}
```
