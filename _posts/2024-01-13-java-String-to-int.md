---
layout: post
title: 문자 정수 변환
categories: Java
date: 2024-01-13 22:12:00 +0900
---
자바에서 문자를 정수로 변환하려면 다음과 같이 한다.

```java
char c = '1';

int num = c - '0';

System.out.println((int)c);    // 49
System.out.println(num);        // 1
```

문자형에서 '0'을 빼면 정수로 변환되는 것이다.

왜 char형하고 char형을 뺐는데 int형이 되는 것일까?

자바에서 연산되는 방식이, '-' 연산은 이항연산자이다. 양쪽을 같게 만들고 (큰 범위를 가진 형으로) 연산을 수행하는 것이다.

char형이 문자형이라고 생각할 수 있지만, 사실 char형은 int형이다.

'-' 연산을 할 때 int형으로 맞춰놓고 계산을 하는 것이다.

정수형끼리의 계산은 int형이 기본이다.

'1'을 정수형으로 변환해서 그대로 출력하면 49가 나온다. 그런데, '0'을 정수형으로 변환해서 그대로 출력하면 48이 나온다. 즉, '1'을 숫자 1로 출력되게 하려면, '0'을 뺀 값을 출력해야 하는 것이다. 이때 '0'이 48, '1'이 49인 것은 ASCII 코드라서 그렇다.

<br>

이것을 적용한 알고리즘 문제로, SWEA 2058. 자릿수 더하기 문제가 있다.

하나의 자연수를 입력 받아 각 자릿수의 합을 계산하는 프로그램을 작성하는 것이다.

답안 코드는 다음과 같다.

```java
import java.util.Scanner;

public class Solution {

	public static void main(String[] args) {

		Scanner sc = new Scanner(System.in);
		int num = sc.nextInt();
		String num_str = String.valueOf(num);
		
		int sum = 0;
		for (int i = 0; i < num_str.length(); i++) {
			sum += (num_str.charAt(i) - '0');
		}
		
		System.out.println(sum);
	}
}
```

위 코드를 보면, sum에 각 자릿수를 정수형으로 변환한 것을 더할 때 각각의 값에서 '0'을 뺀다. (참고로, ```num_str.charAt(i)```는 문자열에서 각 자릿수의 문자 값을 가져오는 것이다.)

만약 각각의 값에서 '0'을 빼지 않으면 훨씬 큰 값이 나오게 된다.

<br>

입력

```6789```

출력

```30```