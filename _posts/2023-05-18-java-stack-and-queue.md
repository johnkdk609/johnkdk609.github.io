---
layout: post
title: Stack과 Queue
description: Java의 정석 기초편 ch. 11 컬렉션 프레임웍
categories: Java
date: 2023-05-18 17:52:00 +09:00
---
스택은 마지막에 저장한 데이터를 가장 먼저 꺼내게 되는 LIFO(Last In First Out)구조로 되어 있고, 큐는 처음에 저장한 데이터를 가장 먼저 꺼내게 되는 FIFO(First In First Out)구조로 되어 있다.

순차적으로 데이터를 추가하고 삭제하는 **스택에는 ArrayList와 같은 배열 기반의 컬렉션 클래스가 적합**하지만, 큐는 데이터를 꺼낼 때 항상 첫 번째 저장된 데이터를 삭제하므로, ArrayList와 같은 배열 기반의 컬렉션 클래스를 사용한다면 데이터를 꺼낼 때마다 빈 공간을 채우기 위해 데이터의 복사가 발생하므로 비효율적이다. 그래서 **큐는 ArrayList보다 데이터의 추가/삭제가 쉬운 LinkedList로 구현하는 것이 더 적합**하다.

> Stack - ArrayList와 같은 배열 기반의 컬렉션 클래스가 적합\
> Queue - LinkedList와 같이 데이터의 추가/삭제가 용이한 것으로  적합

## Stack과 Queue의 메서드

### Stack의 메서드
<table>
  <tr>
    <th>메서드</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>boolean empty()</td>
    <td>Stack이 비어있는지 알려준다.</td>
  </tr>
  <tr>
    <td>Object peek()</td>
    <td>Stack의 맨 위에 저장된 객체를 반환. pop()과 달리 Stack에서 객체를 꺼내지는 않음. <br>(비었을 때는 EmptyStackException 발생)</td>
  </tr>
  <tr>
    <td>Object pop()</td>
    <td>Stack의 맨 위에 저장된 객체를 꺼낸다. (비었을 때는 EmptyStackException 발생)</td>
  </tr>
  <tr>
    <td>Object push(Object item)</td>
    <td>Stack에 객체(item)를 저장한다.</td>
  </tr>
  <tr>
    <td>int search(Object o)</td>
    <td>Stack에 주어진 객체(o)를 찾아서 그 위치를 반환. 못 찾으면 -1을 반환. <br>(배열과 달리 위치는 0이 아닌 1부터 시작. 맨 위의 요소가 1)</td>
  </tr>
</table>


### Queue의 메서드

<table>
  <tr>
    <th>메서드</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>boolean add(Object o)</td>
    <td>지정된 객체를 Queue에 추가한다. 성공하면 true를 반환. 저장 공간이 부족하면 IllegalStateException 발생</td>
  </tr>
  <tr>
    <td>Object remove()</td>
    <td>Queue에서 객체를 꺼내 반환. 비어있으면 NoSuchElementException 발생</td>
  </tr>
  <tr>
    <td>Object element()</td>
    <td>삭제 없이 요소를 읽어온다. peek와 달리 Queue가 비었을 때 NoSuchElementException 발생</td>
  </tr>
  <tr>
    <td>boolean offer(Object o)</td>
    <td>Queue에 객체를 저장. 성공하면 true, 실패하면 false를 반환</td>
  </tr>
  <tr>
    <td>Object poll()</td>
    <td>Queue에서 객체를 꺼내서 반환. 비어있으면 null을 반환</td>
  </tr>
  <tr>
    <td>Object peek()</td>
    <td>삭제 없이 요소를 읽어 온다. Queue가 비어있으며 null을 반환</td>
  </tr>
</table>


## Stack과 Queue 예제

```java
import java.util.*;

class Ex11_2 {
	public static void main(String[] args) {
		Stack st = new Stack();
		Queue q = new LinkedList();	// Queue인터페이스의 구현체인 LinkedList를 사용 
		
		st.push("0");
		st.push("1");
		st.push("2");

		q.offer("0");
		q.offer("1");
		q.offer("2");

		System.out.println("= Stack =");
		while(!st.empty()) {
			System.out.println(st.pop()); // 스택에서 요소 하나를 꺼내서 출력 
		}

		System.out.println("= Queue =");
		while(!q.isEmpty()) {
			System.out.println(q.poll()); // 큐에서 요소 하나를 꺼내서 출력 
		}
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
= Stack =
2
1
0
= Queue =
0
1
2
```

스택과 큐에 각각 "0", "1", "2"를 같은 순서로 넣고 꺼내었을 때의 결과가 다른 것을 알 수 있다. 큐는 먼저 넣은 것이 먼저 꺼내지는 구조(FIFO)이기 때문에 넣을 때와 같은 순서이고, 스택은 먼저 넣은 것이 나중에 꺼내지는 구조(LIFO)이기 때문에 넣을 때의 순서와 반대로 꺼내진 것을 알 수 있다. 

자바에서는 스택을 Stack클래스로 구현하여 제공하고 있지만 큐는 Queue인터페이스로만 정의해 놓았을 뿐 별도의 클래스를 제공하고 있지 않다. 대신 Queue인터페이스를 구현한 클래스들이 있어서 이들 중의 하나를 선택해서 사용하면 된다.


## 인터페이스를 구현한 클래스 찾기

위의 예제에서와 같이 Queue인터페이스의 기능을 사용하고자 할 때는 다음과 같이 Java API 문서를 참고하면 된다. 아래 그림의 하단에 보면 'All Known Implementing Classes'라는 항목이 있는데 여기에 나열된 클래스들이 바로 Queue인터페이스를 구현한 클래스들이다.

<img width="1100" alt="[자바의 정석 - 기초편] ch11-15~18 Stack과 Queue" title="Java API문서에서 찾은 Queue" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/827c1fac-6f18-4f55-9747-1f519780f65a">

각 클래스들은 각자 나름대로의 용도가 있겠지만 적어도 Queue인터페이스에 정의된 메서드를 모두 작성해 놓았으며, 이 메서드들에 대해서는 내용은 좀 다를 수 있겠지만 대부분 거의 같은 기능을 한다. 그래서 Queue인터페이스에 정의된 기능을 사용하고 싶다면, 'All Known Implementing Classes'에 적혀있는 클래스들 중에서 적당한 것을 하나 골라잡아서 'Queue q = new LinkedList();'와 같은 식으로 객체를 생성해서 사용하면 된다.


## Stack과 Queue의 활용

우리가 쉽게 찾아볼 수 있는 스택과 큐의 활용 예는 다음과 같다.

> **스택의 활용 예** - 수식계산, 수식괄호검사, 워드프로세서의 undo/redo, 웹브라우저의 뒤로/앞으로\
> **큐의 활용 예** - 최근사용문서, 인쇄작업 대기목록, 버퍼(buffer)


## Stack과 Queue의 활용 예제 1

```java
import java.util.*;

public class Ex11_3 {
	public static void main(String[] args) {
		if (args.length != 1) {
			System.out.println("Usage:java Ex11_3 \"EXPRESSION\"");
			System.out.println("Example:java Ex11_3 \"((2+3)*1)+3\"");
			System.exit(0);
		}

		Stack st = new Stack();
		String expression = args[0];

		System.out.println("expression:" + expression);

		try {
			for (int i = 0; i < expression.length(); i++) {
				char ch = expression.charAt(i);

				if (ch == '(') {
					st.push(ch + "");
				} else if (ch == ')') {
					st.pop();
				}
			}

			if (st.isEmpty()) {
				System.out.println("괄호가 일치합니다.");
			} else {
				System.out.println("괄호가 일치하지 않습니다.");
			}
		} catch (EmptyStackException e) {
			System.out.println("괄호가 일치하지 않습니다.");
		} // try
	}
}
```

위 코드에서 Arguments에 아무 것도 적혀있지 않다면 출력 결과는 다음과 같다.

```
Usage:java Ex11_3 "EXPRESSION"
Example:java Ex11_3 "((2+3)*1)+3"
```

위 코드에서 Arguments에 (2+3)\*1이 적혀있다면 출력 결과는 다음과 같다.

```
expression:(2+3)*1
괄호가 일치합니다.
```

입력한 수식의 괄호가 올바른지를 체크하는 예제이다. '('를 만나면 스택에 넣고 ')'를 만나면 스택에서 '('를 꺼낸다. ')'를 만나서 '('를 꺼내려 할 때 스택이 비어있거나 수식을 검사하고 난 후에도 스택이 비어있지 않으면 괄호가 잘못된 것이다.

')'를 만나서 '('를 꺼내려 할 때 스택이 비어있으면 EmptyStackException이 발생하므로 try-catch문을 이용해서 EmptyStackException이 발생하면 괄호가 일치하지 않는다믄 메시지를 출력하도록 했다.


## Stack과 Queue의 활용 예제 2

```java
import java.util.*;

class Ex11_4 {
	static Queue q = new LinkedList();
	static final int MAX_SIZE = 5;	// Queue에 최대 5개까지만 저장되도록 한다. 

	public static void main(String[] args) {
		System.out.println("help를 입력하면 도움말을 볼 수 있습니다.");

		while(true) {
			System.out.print(">>");
			try {
				// 화면으로부터 라인 단위로 입력 받는다. 
				Scanner s = new Scanner(System.in);  
				String input = s.nextLine().trim();

				if("".equals(input)) continue;

				if(input.equalsIgnoreCase("q")) {
					System.exit(0);
				} else if(input.equalsIgnoreCase("help")) {
					System.out.println(" help - 도움말을 보여줍니다.");
					System.out.println(" q 또는 Q - 프로그램을 종료합니다.");
					System.out.println(" history - 최근에 입력한 명령어를 " + MAX_SIZE +"개 보여줍니다.");
				} else if(input.equalsIgnoreCase("history")) {
					int i=0;
					// 입력 받은 명령어를 저장하고, 
					save(input);    

					// LinkedList의 내용을 보여준다. 
					LinkedList tmp = (LinkedList)q;
					ListIterator it = tmp.listIterator();

					while(it.hasNext())
						System.out.println(++i+"."+it.next());
				} else {
					save(input);    
					System.out.println(input);
				} // if(input.equalsIgnoreCase("q")) {
			} catch(Exception e) {
				System.out.println("입력 오류입니다.");
			}
		} // while(true)
	} //  main()
	public static void save(String input) {
		// queue에 저장한다. 
		if(!"".equals(input))
          q.offer(input);

		// queue의 최대 크기를 넘으면 제일 처음 입력된 것을 삭제한다. 
		if(q.size() > MAX_SIZE)  // size()는 Collection인터페이스에 정의 
			q.remove();
	}
} // end of class
```

위 코드의 출력 결과 예시는 다음과 같다.

```
help를 입력하면 도움말을 볼 수 있습니다.
>>help
 help - 도움말을 보여줍니다.
 q 또는 Q - 프로그램을 종료합니다.
 history - 최근에 입력한 명령어를 5개 보여줍니다.
>>dir
dir
>>cd
cd
>>mkdir
mkdir
>>dir
dir
>>history
1.dir
2.cd
3.mkdir
4.dir
5.history
>>q
```

이 예제는 유닉스의 history명령어를 Queue를 이용해서 구현한 것이다. history명령어는 사용자가 입력한 명령어의 이력을 순서대로 보여준다. 여기서는 최근 5개의 명령어만을 보여주는데 MAX_SIZE의 값을 변경함으로써 더 많은 명령어 입력 기록을 남길 수 있다.

대부분의 프로그램이 최근에 열어 본 문서들의 목록을 보여주는 기능을 제공하는데, 이 기능도 위의 예제를 응용하면 쉽게 구현할 수 있을 것이다.
