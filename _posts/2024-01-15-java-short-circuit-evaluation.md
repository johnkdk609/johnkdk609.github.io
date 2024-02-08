---
layout: post
title: Short Circuit Evaluation
categories: Java
date: 2024-01-15 23:33:00 +0900
---
자바의 논리 연산자에는 '&&'와 '&#124;&#124;'가 있다.

* && - 피연산자 모두가 true일 경우에만 true
* &#124;&#124; - 피연산자 중 하나라도 true일 경우 true

<br>

이때, 자바에는 <b>"Short Circuit Evaluation"</b>이라는 것이 작동한다.

* && 연산일 경우 - 만약 앞에 있는 것이 false라면, 뒤에 것은 볼 것도 없이 전체 결과가 false가 되는 것이다. 즉, 뒤는 고려하지 않고 바로 결과를 도출한다.
* &#124;&#124; 연산일 경우 - 앞이 true라면, 어차피 전체 결과가 true이다. 그러므로 뒤는 평가하지 않고 바로 결과를 도출한다.

<br>

만약 &, &#124;가 한 번씩만 있으면, Short Circuit Evaluation이 적용되지 않는다.

<br>

다음 코드를 보면 이해가 잘 될 것이다.

```java
int a = 15;
int b = 10;

System.out.println((a += 10) > 20 | (b -= 5) > 10);
System.out.println(a);
System.out.println(b);
```

위 코드의 출력 결과는 어떻게 될까?

```
true
25
5
```

우선, 논리 연산자가 '&#124;'이니, ```(a += 10) > 20```이 true면 ```(b -= 5) > 10```가 false인 것과 상관없이 전체 결과는 true이다. 그리고, '&#124;'가 한 번만 쓰였으므로 a는 25, b는 5가 된다.

<br>

반면 다음 코드를 보자.

```java
int a = 15;
int b = 10;

System.out.println((a += 10) > 20 || (b -= 5) > 10);
System.out.println(a);
System.out.println(b);
```

위 코드의 출력 결과는 어떻게 될까?

```
true
25
10
```

<br>

이전 코드와 다른 점은 논리 연산자가 수직선('&#124;') 1개에서 2개로 바뀌었다는 것이다. 그런데 b의 값이 10으로, ```b -= 5```가 적용되지 않은 것을 알 수 있다.

이는 수직선이 2개 사용되어서, ```(a += 10) > 20```이 true이므로 Short Circuit Evaluation이 적용되었고, 결론적으로 ```(b -= 5) > 10```이 실행되지 않고 종료된 것이다. 다시 말해, ```b -= 5```가 아예 실행되지 않은 것이다.

<br>
<br>

Short Circuit Evaluation을 제대로 고려하지 않아 계속 문제를 해결하지 못한 경험이 있다.

SWEA1223 계산기2 문제이다.

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Stack;
import java.util.StringTokenizer;

public class SWEA_1223_계산기2 {

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st;
		for (int tc = 1; tc <= 10; tc++) {
			st = new StringTokenizer(br.readLine());
			Integer.parseInt(st.nextToken());
			st = new StringTokenizer(br.readLine());
			String calfor = st.nextToken();
			
			String converted = convert(calfor);
			int result = calculate(converted);
			System.out.printf("#%d %s\n", tc, result);
		}
	}
	
	public static String convert(String calfor) {
		
		Stack<Character> stack = new Stack<>();
		String converted = "";
		for (int i = 0; i < calfor.length(); i++) {
			char token = calfor.charAt(i);
			
			if (token != '+' && token != '*') {
				converted += token;
			} else {
				if (stack.isEmpty()) {
					stack.push(token);
				} else {
					
					if (token == '+' && stack.peek() == '*') {
						while (stack.peek() == '*' && !stack.isEmpty()) {
							converted += stack.pop();
						}
						if (stack.peek() == '+' && !stack.isEmpty()) {
							converted += stack.pop();
						}
						stack.push(token);
					} else if (token == '+' && stack.peek() == '+') {
						converted += stack.pop();
						stack.push(token);
					} else if (token == '*' && stack.peek() == '*') {
						converted += stack.pop();
						stack.push(token);
					} else {
						stack.push(token);
					}
						
				}
			}
			
		}
		while (!stack.isEmpty()) {
			converted += stack.pop();
		}
		
		return converted;
		
	}
	
	public static int calculate(String converted) {
		Stack<Integer> stack = new Stack<>();
		
		for (int i = 0; i < converted.length(); i++) {
			char token = converted.charAt(i);
			
			if (token != '+' && token != '*') {
				stack.push(token - '0');
			} else {
				int n2 = stack.pop();
				int n1 = stack.pop();
				if (token == '*') {
					stack.push(n1 * n2);
				} else if (token == '+') {
					stack.push(n1 + n2);
				}
			}
		}
		
		return stack.pop();
	}
}
```

위와 같은 코드에 입력값으로는 다음과 같이 넣었다.

```
101
9+5*2+1+3*3*7*6*9*1*7+1+8*6+6*1*1*5*2*4*7+4*3*8*2*6+7*8*4*5+3+7+2+6+5+1+7+6+7*3*6+2+6+6*2+4+2*2+4*9*3
79
4+4*3*4*9+2+7*4*7+7*7*9*5*2+8*8+2*6*7*3*7*9*3*4+8+8*9+3+9+6+9+4*1+6*3+5+1+7+5*1
113
2+3+9*9+8+2*1+3*2*3*1+3*3+1+2+3*6*2*7*4+9+1+4+6+9*9*5+7+8+6+3+9*2+1+7+4+3+9*3*1+4*4+4*3*1+9*3+9*5*1*7*8+2+8+8*7+9
89
4*9+1+1*8+8*9*7+1*4*5*2*5+8*3*5+5+2*4+2+8+6*2*2+9+3*1*2+2*5+9*2*3*9+6+7*9+9*4+7+6+6*6+3+8
77
5+4+9+9*9*2+6*6*5+9+3*5+5*7*8*3*7*1*9*9+8+3+8*9*6+2*9*3+6*5+6*7*2+5+5*3+4*6+7
119
5+7+1+6+3+6*7+7+5*5*3*5*6*9+5*9*5*9+8+8+5*1*6*2+3+2+8+6+2+2*3*4+5*8*3*6*2*9+1*7*7*4*2+2*5+6+7+2*7*4+9*6*4*3*1*3*5+3*7+8
115
8*6+3*9*8*7*6*3+5*7*6*6+3*5+2*4*9*3+5+2+1*4*1*7+6*8+9+3+2+8*3+8*2+6*9+2*2*7+8*1*2+9*3+1+5*5*8+4*1*2*4*2*6*3*8*8+4+1
91
5*8*4+5*7+9*2+6+5*7+1*7*9+8+6*1*2+7+5*9*6*3+4*8*9*6*1*3+7*1+2+5+1*4*9+6*4+7*1*2*4*2+3+3*4+9
107
7*1+7+5+3*7*1*7+8*3*8+7+3*2*6*2+3+6*4+3+8+9*4+1+5*7*8+9+1+2+5+6*7+4*5*2+4+8*4+7+9*1*3*1+1*2*8+3+2+9*1*5*9+7
109
1+1+7+3*2+1+3*7*8+9*6+1+8*3*7+8*5*7*7+4*3*7*4+7+3+2*2+7+8*8*6+6*6*7+7*1*5*7+3+1*5+1*8*4+9+6+7*5+3+1*8*8*9+4+7
```

출력값은 다음과 같아야 한다.

```
#1 28134
#2 195767
#3 4293
#4 1592
#5 477326
#6 45647
#7 102951
#8 6548
#9 1394
#10 4285
```

<br>

그런데 계속 다음과 같은 메세지가 출력됐다.

```
#1 28134
#2 195767
#3 4293
Exception in thread "main" java.util.EmptyStackException
	at java.base/java.util.Stack.peek(Stack.java:101)
	at SWEA_1223_계산기2.convert(SWEA_1223_계산기2.java:39)
	at SWEA_1223_계산기2.main(SWEA_1223_계산기2.java:18)
```

답이 잘 나오다가 위와 같은 오류가 발생하는 것이다. EmptyStackException으로, 보아하니 ```stack.peek()```를 호출할 때 stack이 비어있어서 오류가 발생하는 것이었다.

```java
while (stack.peek() == '*' && !stack.isEmpty()) {
    converted += stack.pop();
}
if (stack.peek() == '+' && !stack.isEmpty()) {
    converted += stack.pop();
}
stack.push(token);
```

이 문제를 해결하기 위해, 위 코드를 다음과 같이 수정했다.

```java
while (!stack.isEmpty() && stack.peek() == '*') {	// stack이 비어있지는 않은지부터 확인
    converted += stack.pop();
}
if (!stack.isEmpty() && stack.peek() == '+') {		// stack이 비어있지는 않은지부터 확인
    converted += stack.pop();
}
stack.push(token);
```

<br>

stack이 비어있는지 여부를 앞부분에 위치시켜서, ```stack.peek() == '*'```와 같은 로직을 검토하기보다, ```!stack.isEmpty()```를 먼저 검토하는 것이다.

이렇게 하니 문제가 해결되었다. 자바의 Short Circuit Evaluation 개념을 활용하지 못해 계속 문제에 봉착했던 것이다.
