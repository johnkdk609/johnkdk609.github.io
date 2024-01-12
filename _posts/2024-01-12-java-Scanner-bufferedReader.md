---
layout: post
title: Scanner와 BufferedReader
categories: Java
date: 2024-01-12 23:43:00 +0900
---
자바에서 입력을 받는 방법은 여러 가지가 있다.

그 중 가장 많이 사용되는 것이 Scanner이다.

## Scanner

```java
Scanner sc = new Scanner(System.in);
String str1 = sc.next();
```

와 같은 방식으로 사용할 수 있다. 위 코드는 String을 입력받을 때 주로 사용한다. 이때, ```sc.next();``` 말고도 ```sc.nextLine();```을 사용할 수 있다.

둘의 차이는 다음과 같다.

* ```sc.next();```: Token 단위로 끊어서 입력을 받는다. (즉, 공백으로 끊어서 받는다는 것)
* ```sc.nextLine();```: 줄 단위로(\n) 끊어서 입력을 받는다.

<br>

숫자형을 받아올 때에는 다음과 같이 한다.

```java
int num1 = sc.nextInt();
```

위 방법을 쓰면 한 번에 두 가지 기능이 사용되고 있다.

1. 토큰 단위로 끊어서 받고
2. 문자열을 int형으로 변환해서 가져옴

즉, 기본적으로 문자열을 받는 것이고 위와 같이 .nextInt() 을 쓰면 int형으로 parse까지 해주는 것이다.

<br>

예시를 들면 다음과 같다.

만약 input으로 "I am a programmer"라고 입력하면, ```sc.next();``` 방식일 경우 토큰 단위로 끊어서 받으니 "I"까지만 받는다. "I"와 "am" 사이에 공백이 있기 때문이다. 

반면, ```sc.nextLine();``` 방식일 경우 "I am a programmer"을 다 받는 것이다.

## BufferedReader

또, BufferedReader을 이용할 수 있다. 사용 방법은 다음과 같다.

```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
String str2 = br.readLine();
```

이때, 'add throws declaration'을 클릭하여 IOException을 처리해야 한다.

안타깝게도 BufferedReader에는 토큰 단위로 끊어서 가져오는 기능이 없다. 또, 형변환 기능도 없다.

문자열로만 가져올 수 있고, 한 줄 단위로만 가져올 수 있다.

<br>

만약 토큰 단위로 하나씩 끊어서 사용하려면, StringTokenizer을 이용해야 한다.

```java
StringTokenizer st = new StringTokenizer(str2);
```

앞서 BufferedReader로 입력받은 str2를 위와 같이 StringTokenizer로 처리하면, 토큰으로 끊어준다. 

만약 토큰을 int형으로 변환하고 싶다면 다음 코드를 추가로 입력한다.

```java
int n = Integer.parseInt(st.nextToken());
```

토큰이 여전히 문자열이기 때문에, 이렇게 int로 변환하는 것이다.