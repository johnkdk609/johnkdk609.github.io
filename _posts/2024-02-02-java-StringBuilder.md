---
layout: post
title: StringBuilder
categories: Java
date: 2024-02-02 23:43:00 +0900
---
StringBuilder에 대해 알아보겠다.

StringBuilder는 Java에서 문자열을 처리하고 조작하는 데 사용되는 클래스이다. 이 클래스는 문자열의 변경이 필요한 경우 효율적으로 작업할 수 있게 설계되었다.

String 클래스와 달리, StringBuilder는 가변적인 문자열을 다루기에 더 효율적이다. 예를 들어 문자열을 더하거나 삭제하는 경우, StringBuilder를 사용하면 매번 새로운 문자열을 생성하지 않고 기존 문자열에 직접 수정할 수 있다. 이는 메모리와 시간을 절약하여 프로그램의 성능을 향상시킨다.

StringBuilder 클래스는 문자열을 변경하는 여러 가지 메서드를 제공한다.

예를 들어, append() 메서드는 문자열의 끝에 새로운 문자열을 추가하고, delete() 메서드는 지정된 범위의 문자열을 삭제한다.

예시 코드는 다음과 같다.

```java
package stringbuilder_practice;

public class StringBuilderPractice {

	public static void main(String[] args) {
		StringBuilder sb = new StringBuilder();     // StringBuilder 객체를 생성
		
		sb.append("Hello, ");   // append
		sb.append("SpringBuilder!!");
		
		System.out.println(sb.toString());
		
		sb.delete(5, sb.length() - 1);  // delete
		
		System.out.println(sb.toString());
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
Hello, SpringBuilder!!
Hello!
```

그런데, 위 코드에서 궁금한 점이 있을 수 있다. delete(int start, int end) 메서드를 사용할 때, 파라미터 end에 ```sb.length() - 1```을 넣었는데 마지막 인덱스의 값인 '!' 이 삭제되지 않은 것이다.

이는 Java에서 ```StringBuilder.delete(int start, int end)``` 메서드는 start 인덱스(포함)에서 end 인덱스(제외)까지 문자를 삭제하기 때문이다.