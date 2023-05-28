---
layout: post
title: 2차원 배열
description: Java의 정석 기초편 ch. 5 배열
categories: Java
date: 2023-05-28 11:18:00 +09:00
---
2차원 이상의 배열, 즉 다차원(mutli-dimensional) 배열도 선언해서 사용할 수 있다. 메모리의 용량이 허용하는 한 차원의 제한은 없다. 주로 1, 2차원 배열이 사용되므로 2차원 배열만 잘 이해하고 나면 3차원 이상의 배열도 어렵지 않게 다룰 수 있다.

2차원 배열을 선언하는 방법은 1차원 배열과 같다. 다만 대괄호\[\]가 하나 더 들어갈 뿐이다.

<table>
  <tr>
    <th>선언 방법</th>
    <th>선언 예</th>
  </tr>
  <tr>
    <td>타입[][] 변수이름;</td>
    <td>int[][] score;</td>
  </tr>
  <tr>
    <td>타입 변수이름[][];</td>
    <td>int score[][];</td>
  </tr>
  <tr>
    <td>타입[] 변수이름[];</td>
    <td>int[] score[];</td>
  </tr>
</table>

(3차원 이상의 고차원 배열의 선언은 대괄호\[\]의 개수를 차원의 수만큼 추가해 주기만 하면 된다.)

2차원 배열은 주로 테이블 형태의 데이터를 담는 데 사용되며, 만일 4행 3열의 데이터를 담기 위한 배열을 생성하려면 다음과 같이 한다.

```java
int[][] score = new int[4][3];    // 4행 3열의 2차원 배열을 선언한다.
```

위 문장이 수행되면 아래의 그림처럼 4행 3열의 데이터, 모두 12개의 int값을 저장할 수 있는 공간이 마련된다.

<img width="300" alt="[자바의 정석 - 기초편] ch5-18~20 2차원 배열" title="[자바의 정석 - 기초편] ch5-18~20 2차원 배열" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b4cb2af8-a26d-42b9-a9c0-63814eb2fef7">

위의 그림에서는 각 요소, 즉 저장공간의 타입을 적어놓은 것이고, 실제로는 배열 요소의 타입인 int의 기본값인 0이 저장된다. 배열을 생성하면, 배열의 각 요소에는 배열 요소 타입의 기본값이 자동적으로 저장된다.


## 2차원 배열의 인덱스

2차원 배열은 행(row)과 열(column)로 구성되어 있기 때문에 index도 행과 열에 각각 하나씩 존재한다. '행 index'의 범위는 '0 ~ 행의 길이 - 1'이고 '열 index'의 범위는 '0 ~ 열의 길이 - 1'이다. 그리고 2차원 배열의 각 요소에 접근하는 방법은 '배열이름\[행 index\]\[열 index\]'이다. 

만일 다음과 같이 배열 score를 생성하면, score\[0\]\[0\]부터 score\[3\]\[2\]까지 모두 12개(4×3=12)의 int값을 저장할 수 있는 공간이 마련되고, 각 배열 요소에 접근할 수 있는 방법은 아래의 그림과 같다.

```java
int[][] score = new int[4][3];    // 4행 3열의 2차원 배열 score를 생성
```

<img width="600" alt="[자바의 정석 - 기초편] ch5-18~20 2차원 배열" title="[자바의 정석 - 기초편] ch5-18~20 2차원 배열" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/02f883ad-72f8-4cfd-ad1e-9f1d1e111137">

배열 score의 1행 1열에 100을 저장하고, 이 값을 출력하려면 다음과 같이 하면 된다.

```java
score[0][0] = 100;                  // 배열 score의 1행 1열에 100을 저장
System.out.println(score[0][0]);    // 배열 score의 1행 1열의 값을 출력
```

## 2차원 배열의 초기화

2차원 배열도 괄호{}를 사용해서 생성과 초기화를 동시에 할 수 있다. 다만, 1차원 배열보다 괄호{}를 한 번 더 써서 행별로 구분해 준다.

```java
int[][] arr = new int[][]{ {1, 2, 3}, {4, 5, 6} };
int[][] arr = { {1, 2, 3}, {4, 5, 6} };   // new int[][]가 생략됨 
```

크기가 작은 배열은 위와 같이 간단히 한 줄로 써주는 것도 좋지만, 가능하면 아래와 같이 행별로 줄 바꿈을 해주는 것이 보기도 좋고 이해하기 쉽다.

```java
int[][] arr = {
                {1, 2, 3},
                {4, 5, 6}
              };
```

만일 아래와 같은 테이블 형태의 데이터를 배열에 저장하려면,

<table>
  <tr>
    <th></th>
    <th>국어</th>
    <th>영어</th>
    <th>수학</th>
  </tr>
  <tr>
    <td style="text-align: center; vertical-align: middle;">1</td>
    <td style="text-align: center; vertical-align: middle;">100</td>
    <td style="text-align: center; vertical-align: middle;">100</td>
    <td style="text-align: center; vertical-align: middle;">100</td>
  </tr>
  <tr>
    <td style="text-align: center; vertical-align: middle;">2</td>
    <td style="text-align: center; vertical-align: middle;">20</td>
    <td style="text-align: center; vertical-align: middle;">20</td>
    <td style="text-align: center; vertical-align: middle;">20</td>
  </tr>
  <tr>
    <td style="text-align: center; vertical-align: middle;">3</td>
    <td style="text-align: center; vertical-align: middle;">30</td>
    <td style="text-align: center; vertical-align: middle;">30</td>
    <td style="text-align: center; vertical-align: middle;">30</td>
  </tr>
  <tr>
    <td style="text-align: center; vertical-align: middle;">4</td>
    <td style="text-align: center; vertical-align: middle;">40</td>
    <td style="text-align: center; vertical-align: middle;">40</td>
    <td style="text-align: center; vertical-align: middle;">40</td>
  </tr>
</table>

다음과 같이 하면 된다.

```java
int[][] score = {
                  {100, 100, 100},
                  {20, 20, 20},
                  {30, 30, 30},
                  {40, 40, 40}
                };
```

위 문장이 수행된 후에, 2차원 배열 score가 메모리에 어떤 형태로 만들어지는지 그려보면 다음과 같다.

<img width="600" alt="[자바의 정석 - 기초편] ch5-18~20 2차원 배열" title="[자바의 정석 - 기초편] ch5-18~20 2차원 배열" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c041511f-5e41-4512-9e46-dcbffbcb4ab2">


## 2챠원 배열의 초기화 예제 1

```java
class Ex5_8 {
	public static void main(String[] args) {
		int[][] score = {
				  { 100, 100, 100 },
				  { 20, 20, 20 },
				  { 30, 30, 30 },
				  { 40, 40, 40 }
		};
		int sum = 0;

		for (int i = 0; i < score.length; i++) {
			for (int j = 0; j < score[i].length; j++) {
				System.out.printf("score[%d][%d]=%d%n", i, j, score[i][j]);

				sum += score[i][j];
			}
		}

		System.out.println("sum=" + sum);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
score[0][0]=100
score[0][1]=100
score[0][2]=100
score[1][0]=20
score[1][1]=20
score[1][2]=20
score[2][0]=30
score[2][1]=30
score[2][2]=30
score[3][0]=40
score[3][1]=40
score[3][2]=40
sum=570
```

2차원 배열 score의 모든 요소의 합을 구하고 출력하는 예제이다. 2차원 배열 score를 그림으로 그려보면 아래와 같다.

<img width="650" alt="[자바의 정석 - 기초편] ch5-21~23 2차원배열 예제" title="[자바의 정석 - 기초편] ch5-21~23 2차원배열 예제" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4a0b2724-2e0b-4f70-9893-58fe58097cb5">

그림에서 알 수 있듯이 2차원 배열은 '배열의 배열'로 구성되어 있다. 즉, 여러 개의 1차원 배열을 묶어서 또 하나의 배열로 만든 것이다. 그러면, 여기서 score.length의 값은 얼마일까?

배열 참조변수 score가 참조하고 있는 배열의 길이가 얼마인가를 세어보면 될 것이다. 정답은 4이다. 그리고 score\[0\].length은 배열 참조변수 score\[0\]이 참조하고 있는 배열의 길이이므로 3이다.

같은 이유로 score\[1\].length, score\[2\].length, score\[3\].length의 값 역시 모두 3이다.


## 2차원 배열의 초기화 예제 2

```java
class Ex5_9 {
	public static void main(String[] args) {
		 int[][] score = {
							  { 100, 100, 100 },
							  { 20, 20, 20 },
							  { 30, 30, 30 },
							  { 40, 40, 40 },
							  { 50, 50, 50 }
						};
		// 과목별 총점 
		int korTotal = 0, engTotal = 0, mathTotal = 0;

	    System.out.println("번호  국어  영어  수학  총점  평균 ");
	    System.out.println("=============================");

		for(int i=0;i < score.length;i++) {
			int  sum = 0;      // 개인별 총점 
			float avg = 0.0f;  // 개인별 평균 

			korTotal  += score[i][0];
			engTotal  += score[i][1];
			mathTotal += score[i][2];
			System.out.printf("%3d", i+1);

			for(int j=0;j < score[i].length;j++) {
				sum += score[i][j]; 
				System.out.printf("%5d", score[i][j]);
			}

			avg = sum/(float)score[i].length;  // 평균 계산 
			System.out.printf("%5d %5.1f%n", sum, avg);
		}

		System.out.println("=============================");
     	System.out.printf("총점:%3d %4d %4d%n", korTotal, engTotal, mathTotal);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
번호  국어  영어  수학  총점  평균 
=============================
  1  100  100  100  300 100.0
  2   20   20   20   60  20.0
  3   30   30   30   90  30.0
  4   40   40   40  120  40.0
  5   50   50   50  150  50.0
=============================
총점:240  240  240
```

5명의 학생의 세 과목 점수를 더해서 각 학생의 총점과 평균을 계산하고, 과목별 총점을 계산하는 예제이다.


## 2차원 배열의 초기화 예제 3

```java
import java.util.Scanner;

class Ex5_10{
	public static void main(String[] args) {
		String[][] words = {
			{"chair", "의자"},         // words[0][0], words[0][1]
			{"computer", "컴퓨터"},    // words[1][0], words[1][1]
			{"integer", "정수"}       // words[2][0], words[2][1]
		};

		Scanner scanner = new Scanner(System.in);

		for(int i=0;i<words.length;i++) {
			System.out.printf("Q%d. %s의 뜻은?", i+1, words[i][0]);

			String tmp = scanner.nextLine();

			if(tmp.equals(words[i][1])) {
				System.out.printf("정답입니다.%n%n");
			} else {
			   System.out.printf("틀렸습니다. 정답은 %s입니다.%n%n",words[i][1]);
			}
		} // for
	} // main의 끝 
}
```

위 코드의 출력 결과 예시는 다음과 같다.

```
Q1. chair의 뜻은?dfkajfs
틀렸습니다. 정답은 의자입니다.

Q2. computer의 뜻은?컴퓨터
정답입니다.

Q3. integer의 뜻은?정수
정답입니다.
```

영단어를 보여주고 단어의 뜻을 맞히는 예제이다. words\[i\]\[0\]은 문제이고, words\[i\]\[1\]은 답이다. words\[i\]\[0\]을 화면에 보여주고, 입력 받은 답은 tmp에 저장한다.

```java
System.out.printf("Q%d. %s의 뜻은?", i + 1, words[i][0]);
String tmp = scanner.nextLine();
```

그 다음엔 equals()로 tmp와 words\[i\]\[1\]을 비교해서 정답인지 확인한다.

```java
if (tmp.equals(words[i][1])) {
  System.out.printf("정답입니다.%n%n");
} else {
  System.out.printf("틀렸습니다. 정답은 %s입니다.%n%n", words[i][1]);
}
```
