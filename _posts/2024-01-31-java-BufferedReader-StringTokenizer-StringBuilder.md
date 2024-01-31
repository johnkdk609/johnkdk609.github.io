---
layout: post
title: BufferedReader, StringTokenizer, StringBuilder
categories: Java
date: 2024-01-31 17:08:00 +0900
---
자바의 BufferedReader, StringTokenizer, StringBuilder에 대해 구체적으로 알아보겠다.

## BufferedReader & StringTokenizer

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
import java.util.StringTokenizer;

public class BufferedReaderPractice throws IOException {
    public static void main(String[] args) {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());

        int num1 = Integer.parseInt(st.nextToken());
    }
}
```
