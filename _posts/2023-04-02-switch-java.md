---
layout: post
title: switch 문
categories: Java
date: 2023-04-02 23:26:00 +09:00
description: Java의 정석 기초편 ch. 4 조건문과 반복문
---
if문은 조건식의 결과가 참과 거짓, 두 가지 밖에 없기 때문에 경우의 수가 많아질수록 else-if를 계속 추가해야 하므로 조건식이 많아져서 복잡해지고, 여러 개의 조건식을 계산해야 하므로 처리시간도 많이 걸린다.

이러한 if문과 달리 **switch문**은 단 하나의 조건식으로 많은 경우의 수를 처리할 수 있고, 표현도 간결하므로 알아보기 쉽다.

그래서 처리할 경우의 수가 많은 경우에는 if문보다 switch문으로 작성하는 것이 좋다. 다만 switch문은 제약조건이 있기에, 경우의 수가 많아도 어쩔 수 없이 if문으로 작성해야 하는 경우가 있다.

switch문은 조건식을 먼저 계산한 다음, 그 결과와 일치하는 case문으로 이동한다. 이동한 case문 아래에 있는 문장들을 수행하며, break 문을 만나면 전체 switch문을 빠져나가게 된다.


### switch 문 로직

1. 조건식을 계산한다.
2. 조건식의 결과와 일치하는 case문으로 이동한다.
3. 이후의 문장들을 수행한다.
4. break문이나 switch문의 끝을 만나면 switch문 전체를 빠져나간다.

만일 조건식의 결과와 일치하는 case문이 하나도 없는 경우에는 default문으로 이동한다. default문의 위치는 어디에 두어도 상관없지만, 보통 마지막에 놓기 때문에 break문을 쓰지 않아도 된다.

switch문에서 break문은 각 case문의 영역을 구분하는 역할을 한다. 만약 break문을 생략하면 case문 사이의 구분이 없어지므로 다른 break문을 만나거나 switch문 블럭{}의 끝을 만날 때까지 나오는 모든 문장들을 수행한다. 이러한 이유로 **각 case문의 마지막에는 반드시 break문을 기입해야** 한다.


### switch문의 제약조건

1. switch문의 조건식 결과는 정수 또는 문자열이어야 한다.
2. case문의 값은 정수 상수(문자 포함), 문자열만 가능하며 중복되지 않아야 한다.

같은 값의 case문이 여러 개이면, 어디로 이동해야할 지 알 수 없게 된다. 그리고 변수나 실수는 case문의 값으로 사용할 수 없다.

```java
public static void main(String[] args) {
  int num, result;
  final int ONE = 1;
    ...
  switch (result) {
    case '1':   // OK. 문자 리터럴
    case ONE:   // OK. 정수 상수 (final로 상수화 했기 때문)
    case "YES": // OK. 문자열 리터럴
    case num:   // ERROR. 변수는 불가
    case 1.0:   // ERROR. 실수도 불가
  }
}
```


### switch문 사용 예시 코드

```java
package ch_practice;

import java.util.Scanner;

public class Practice {
	public static void main(String[] args) {		
		
		System.out.print("현재 월을 입력하세요.>");
		
		Scanner scanner = new Scanner(System.in);
		int month = scanner.nextInt();	// 화면을 통해 입력 받은 숫자를 month에 저장 
		
		switch (month) {
			case 3:
			case 4:
			case 5:
				System.out.println("현재의 계절은 봄입니다.");
				break;
			case 6: case 7: case 8:
				System.out.println("현재의 계절은 여름입니다.");
				break;
			case 9: case 10: case 11:
				System.out.println("현재의 계절은 가을입니다.");
				break;
			case 12: case 1: case 2:
				System.out.println("현재의 계절은 겨울입니다.");
				break;
			default:
				System.out.println("월을 제대로 입력하세요.");
		}
	}
}
```

현재 몇 월인지 입력 받아서 해당하는 계절을 출력하는 예제이다. case문은 한 줄에 하나씩 쓰든, 한 줄에 붙여 쓰든 상관없다.
