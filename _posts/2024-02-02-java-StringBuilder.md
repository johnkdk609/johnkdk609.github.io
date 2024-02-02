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

이는 Java에서 ```StringBuilder.delete(int start, int end)``` 메서드는 start 인덱스(포함)에서 end 인덱스(제외)까지 문자를 삭제하기 때문이다. 따라서 'StringBuilder'의 마지막 인덱스까지 문자를 삭제하려면 end 인덱스로 ```sb.length() - 1```이 아닌, ```sb.length()```를 사용해야 한다.

<br>

이런 StringBuilder를 사용할 수 있는 몇 가지 일반적인 시나리오는 다음과 같다.

* <b>루프 내 문자열 연결</b>

루프 내에서 여러 문자열을 연결해야 하는 경우 불필요한 중간 문자열 객체 생성을 방지하기 때문에 StringBuilder를 사용하는 것이 '+' 연산자나 String.concat() 메서드를 사용하는 것보다 효율적일 수 있다.

* <b>복잡한 문자열 작성</b>

특정 조건이나 반복을 기반으로 다양한 구성 요소를 추가하여 문자열을 동적으로 구성해야 하는 경우 StringBuilder는 이를 효율적으로 수행할 수 있는 편리한 방법을 제공한다.

* <b>문자열 수정</b>

하위 문자열을 바꾸거나 특정 위치에 문자열을 삽입하는 등 문자열을 자주 수정해야 하는 경우 StringBuilder를 사용하면 매번 새 문자열 개체를 만들지 않고도 이를 수행할 수 있다.

* <b>성능 고려 사항</b>

문자열 조작 작업이 자주 발생하거나 큰 문자열이 포함되는 성능이 중요한 애플리케이션에서 StringBuilder를 사용하면 불변 String 클래스를 사용할 때보다 성능이 향상될 수 있다. StringBuilder는 변경 가능한 버퍼에서 작동하기 때문이다.

<br>

StringBuilder을 사용하는 예시 코드를 좀 더 알아보겠다.

input.txt라는 파일을 받아오는 상황을 가정해보겠다. input.txt 파일의 내용은 다음과 같다.

```txt
abc
def
ghi
jkl
mno
pqr
stu
vwx
yz
```

```java
import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStreamReader;

public class StringBuilderPractice2 {
	public static void main(String[] args) throws IOException {
		
		BufferedReader br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));
		
		StringBuilder sb = new StringBuilder(); // 여러줄을 모아서 하나의 긴 문자열 만들기
		String str = null; // 한줄씩 읽어온 것을 임시저장할 변수
		
		while((str = br.readLine())!= null) { // 한 줄을 읽어 왔다면
			sb.append(str);
		}
		// sb 안에 긴 문자열이 저장되어 있음.

        System.out.println(sb.toString());
    }
}
```

위 코드의 출력 결과는 다음과 같다.

```txt
abcdefghijklmnopqrstuvwxyz
```

위 코드에서처럼, StringBuilder sb를 생성한 다음, str이라는 String 변수를 생성 및 초기화 했다. 그리고, BufferedReader로 한 줄씩 읽어온 자료를 str에 담은 다음, sb에 append()로 담은 것이다.

이렇게 하면 여러 줄로 이루어져 있는 String 자료를 sb에 길게 한 줄로 옮겨담을 수 있다.

<br>

이렇듯 StringBuilder은 문자열을 동적으로 조작해야 할 때 유용한 클래스이며, 성능을 향상시킬 때 도움이 된다.