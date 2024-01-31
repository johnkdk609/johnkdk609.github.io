---
layout: post
title: BufferedReader, StringTokenizer
categories: Java
date: 2024-01-31 17:08:00 +0900
---
자바의 BufferedReader, StringTokenizer에 대해 구체적으로 알아보겠다.

## BufferedReader

Input을 받아올 때, Scanner을 사용할 수 있다. 하지만, Scanner은 속도가 느리기 때문에 input의 양이 어느 정도 이상 커지면 효율적이지 못하다.

그렇기 때문에 BufferedReader을 사용한다.

<br>

BufferedReader로 데이터를 받아오면, 한 줄 단위로 String으로 받아온다.

* 줄 단위로 받아옴
* String으로 받아옴

이는 Scanner이 토큰 단위로 가져올 수 있고, ```sc.nextInt();```와 같은 방식으로 원하는 자료형으로 casting이 동시에 되는 것과는 다른 양상이다.

그렇기 때문에, StringTokenizer을 사용해 토큰 단위로 끊어야 하고, 직접 ```Integer.parseInt()```를 입력해 파싱(parsing)해야 한다.

<br>

다음 예시 코드를 보자.

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class BufferedReaderPractice {

	public static void main(String[] args) throws IOException {
		
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		
		System.out.println(br.readLine() instanceof String);
	}
}
```

위 코드에서 입력값은 ```br.readLine()```에 들어간다. 만약 숫자 10000을 입력값으로 넣으면, 위 코드의 출력값은 true가 된다. 즉, 10000이 String으로 변한 것이다.

<br>

다음 코드를 보자.

```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

System.out.println(br.readLine());
```

위 코드에 ```1 2 3 4 5```로 1부터 5까지 한 칸씩 띄운 채 입력하면, 출력값은 "1 2 3 4 5"가 나온다. 즉, BufferedReader을 사용하면 한 줄이 String으로 받아지는 것이다.

<br>

그런데 나는 빈 칸을 기준으로 값을 끊어서 받고 싶다. 이런 경우 StringTokenizer을 사용해야 한다.

<br>

## StringTokenizer

만약 BufferedReader을 사용하고 있는데, 입력값에 빈 칸이 없는 숫자를 입력한다면, 그냥 다음과 같이 파싱하면 된다.

```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

int num1 = Integer.parseInt(br.readLine());
```

<br>

그런데, 여러 숫자들을 빈 칸으로 구분해 한 줄로 받아오고, 그것을 토큰 단위(빈 칸으로 구분되는 것)로 끊어주고 싶다면 StringTokenizer을 사용해야 한다.

사용 방법은 다음과 같다.

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class BufferedReaderPractice {

	public static void main(String[] args) throws IOException {
		
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		
		StringTokenizer st = new StringTokenizer(br.readLine());
		
		// 5개의 입력값 빈 칸으로 구분한 채 입력받기
		
		// 길이 5의 int 배열 생성
		int[] arr = new int[5];
		
		// 생성한 배열 arr에 Integer로 파싱한 토큰 순서대로 할당
		for (int i = 0; i < arr.length; i++) {
			arr[i] = Integer.parseInt(st.nextToken());
		}
		
		// 출력
		System.out.println(Arrays.toString(arr));
	}
}
```

위 코드를 실행하고, ```1 2 3 4 5```를 입력하면, 출력값으로 ```[1, 2, 3, 4, 5]```를 받는다.

우선 StringTokenizer st를 생성하고, ```new StringTokenizer(br.readLine())```을 통해 BufferedReader로 받아온 br.readLine()을 StringTokenizer에 넣어 st에 담는다.

그리고 ```Integer.parseInt(st.nextToken())``` 부분의 코드를 통해, ```nextToken()``` 즉, 받아온 데이터를 토큰 단위로 끊어서 각각 배열에 넣는 것이다.
