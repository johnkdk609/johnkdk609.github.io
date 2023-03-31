---
layout: post
title: 전위형(prefix) 증감 연산자와 후위형(postfix) 증감 연산자
categories: Java
data: 2023-03-31 23:11:00 +09:00
description: Java의 정석 기초편 ch. 3 연산자
---
증감 연산자는 피연산자에 저장된 값을 1 증가 또는 감소시킨다. 증감 연산자의 피연산자로 정수와 실수가 모두 가능하지만, 상수는 값을 변경할 수 없으므로 가능하지 않다.

일반적으로 단항 연산자는 피연산자의 왼쪽에 위치하지만, 증가 연산자 '++'와 감소 연산자 '--'는 양쪽 모두 가능하다.

피연산자의 왼쪽에 위치하면 **'전위형(prefix)'**, 오른쪽에 위치하면 **'후위형(postfix)'**이라고 한다.


#### 전위형
* 값이 참조되기 전에 증가시킨다.
* 사용 예: j = ++i;


#### 후위형
* 값이 참조된 후에 증가시킨다.
* 사용 예: j = i++;


만약 '++i;'와 'i++;'처럼 증감연산자가 수식이나 메서드 호출에 포함되지 않고 독립적인 하나의 문장으로 쓰인 경우에는 전위형과 후위형의 차이가 없다.

> ++i;  // 전위형. i의 값을 1 증가시킨다.\
> i++;  // 후위형. 위의 문장과 차이가 없다.


예시 코드를 하나 보겠다.

```java
package ch_practice;

public class Practice {
	public static void main(String[] args) {		
		int i = 5, j = 0;
		
		j = i++;
		System.out.println("j=i++; 실행 후, i=" + i + ", j=" + j);
		
		i = 5;	// 결과를 비교하기 위해, i와 j의 값을 다시 5와 0으로 변경 
		j = 0;
		
		j = ++i;
		System.out.println("j=++i; 실행 후, i=" + i + ", j=" + j);
	}
}
```

위 코드의 실행 결과는 이러하다.


<img width="332" alt="image" src="https://user-images.githubusercontent.com/88493727/229147305-ffb488a2-9295-41c7-9f3c-aecead2ab00d.png">


실행결과를 보면 i의 값은 두 경우 모두 1이 증가되어 6이 되지만, j의 값은 그렇지 않다.

식을 계산하기 위해서는 식에 포함된 변수의 값을 읽어와야 하는데, 전위형은 변수(피연산자)의 값을 먼저 증가시킨 수에 변수의 값을 읽어오는 반면, 후위형은 변수의 값을 먼저 읽어온 후에 값을 증가시킨다.

즉 전위형의 경우 증감 연산자를 식의 이전으로,

```java
j = ++i;  // 전위형

++i;  // 증가 후에
j = i;  // 참조하여 대입
```

반면 후위형은 증감 연산자를 식의 이후로 떼어내면 된다.

```java
j = i++;  // 후위형

j = i;  // 참조하여 대입 후에
i++;  // 증가
```


이번에는 메서드 호출에 증감 연산자가 사용된 예를 보겠다.

```java
package ch_practice;

public class Practice {
	public static void main(String[] args) {		
		int i = 5, j = 5;
		
		System.out.println(i++);	// i의 값을 출력 후, 1 증가 
		System.out.println(++j);	// j의 값을 1 증가 후, 출력 
		System.out.println("i = " + i + ", j = " + j);
	}
}
```

이 코드의 출력값은 다음과 같다.

<img width="175" alt="image" src="https://user-images.githubusercontent.com/88493727/229149784-031ebb7a-7027-4e68-86be-27831e0f2a79.png">

public class Practice {
