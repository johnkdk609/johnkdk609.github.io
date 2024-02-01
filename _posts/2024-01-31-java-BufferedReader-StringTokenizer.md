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

<br>

## BufferedReader와 StringTokenizer의 특징

BufferedReader을 사용하려면 다음과 같이 br을 선언하고 생성해야 한다.

```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
```

비슷한 방식으로 StringTokenizer을 사용하려면 다음과 같이 해야 한다.

```java
StringTokenizer st = new StringTokenizer(br.readLine());
```

그런데 입력값이 여러 줄일 경우, BufferedReader는 전체 코드에서 한 번만 불어오면 되지만, StringTokenizer은 한 줄마다 불러와야 한다는 차이가 있다.

가령, 5번에 걸쳐 입력값을 받아온다고 하면, 다음과 같이 해야 한다.

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class BufferedReaderPractice {

	public static void main(String[] args) throws IOException {
		
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));	// BufferedReader은 맨 위에서 한 번만 호출
		
		String[] str = new String[5];
		
		StringTokenizer st = new StringTokenizer(br.readLine());
		str[0] = st.nextToken();
		
		st = new StringTokenizer(br.readLine());	// st 재활용
		str[1] = st.nextToken();
		
		st = new StringTokenizer(br.readLine());
		str[2] = st.nextToken();

		st = new StringTokenizer(br.readLine());
		str[3] = st.nextToken();
		
		st = new StringTokenizer(br.readLine());
		str[4] = st.nextToken();
		
		System.out.println(Arrays.toString(str));
	}
}
```

위 코드를 보면, BufferedReader은 맨 위에 한 번만 불러졌지만, StringTokenizer은 한 줄 받아올 때마다 생성해야 하는 것을 알 수 있다. (물론, 반복문을 사용할 수 있기는 하지만 st가 재활용되는 것을 보기 위해 위와 같이 일일이 넣었다.)

BufferedReader br은 스트림으로 읽어오기 위한 도구이고, StringTokenizer st는 실제로 한 줄 읽어온 것을 토큰으로 끊기 위해 담은 것이라는 점에서 차이가 있는 것이다.

<br>

또 다른 특징으로는, ```br.readLine()```을 할 경우 한 줄의 String이 담기기는 하는데, 양쪽 끝에 있는 공백도 String의 일부로 담겨오는 경우가 많다는 것이다.

알고리즘 문제풀이를 하다 보면, 입력값으로 받아온 데이터의 오른쪽 끝에 빈 칸이 있어 그로 인해 입력 오류가 발생하는 경우가 빈번하다.

그래서 이러한 현상을 방지하고자, 토큰 단위로 끊을 필요가 없는 한 개짜리 데이터라도 StringTokenizer을 사용할 수 있다.

가령 "abc " 라는 데이터를 입력 받았을 때, 그냥 ```String str1 = br.readLine()```의 방식으로 받아오면 str1에 "abc "가 담긴다. 뒤에 빈 칸 하나까지 다 담기는 것이다.

```java
StringTokenizer st = new StringTokenizer(br.readLine());
String str2 = st.nextToken();
```

하지만, 위와 같이 StringTokenizer을 사용해 str2에 입력값을 담으면, "abc"가 담기는 것이다.

즉, <b>BufferedReader을 사용할 때에는 StringTokenizer을 사용하는 것이 권장된다.</b>

<br>

## BufferedReader로 파일 읽어오기

BufferedReader로 파일을 읽어올 수도 있다.

만약 내가 다음과 같이 10개의 숫자들이 담긴 input.txt 파일을 읽어오고 싶다고 하자.

```txt
12 34 56 78 910 1112 1314 1516 1718 1920
```

이 내용을 읽어와 길이가 10인 배열에 넣고, 그 배열을 출력하고 싶다면 다음과 같이 코드를 작성하면 된다.

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.Arrays;
import java.util.StringTokenizer;

public class BufferedReaderPractice {

	public static void main(String[] args) throws IOException {
		
		BufferedReader br = new BufferedReader(new FileReader("input.txt"));
		
		int[] arr = new int[10];
		
		StringTokenizer st = new StringTokenizer(br.readLine());
		for (int i = 0; i < arr.length; i++) {
			arr[i] = Integer.parseInt(st.nextToken());
		}
		
		System.out.println(Arrays.toString(arr));
	}
}
```

```BufferedReader br = new BufferedReader(new FileReader("input.txt"));```의 방식으로 파일을 읽고, 그 파일 안에 있는 내용을 토큰 단위로 끊은 다음, 생성한 길이 10의 배열 arr에 순서대로 넣는 것이다.

이때 input.txt 파일은 BufferedReaderPractice클래스가 있는 프로젝트 파일에 위치해야 한다.

<br>

이렇게 BufferedReader의 다양한 기능을 활용하면 편리하게, 효과적으로 입력값을 받아올 수 있다.
