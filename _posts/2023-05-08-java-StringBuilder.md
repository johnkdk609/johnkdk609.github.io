---
layout: post
title: StringBuilder
description: Java의 정석 기초편 ch. 9 java.lang패키지와 유용한 클래스
categories: Java
date: 2023-05-08 11:13:00 +09:00
---
StringBuffer는 멀티쓰레드에 안전(thread safe)하도록 동기화되어 있다. 동기화는 StringBuffer의 성능을 떨어뜨린다. 멀티쓰레드로 작성된 프로그램이 아닌 경우, StringBuffer의 동기화는 불필요하게 성능만 떨어트린다.

그래서 **StringBuffer에서 쓰레드의 동기화만 뺀 StringBuilder가 새로 추가**되었다.

StringBuilder는 StringBuffer와 완전히 똑같은 기능으로 작성되어 있어서, 소스코드에서 StringBuffer 대신 StringBuilder를 사용하도록 바꾸기만 하면 된다. 즉, StringBuffer타입의 참조변수를 선언한 부분과 StringBuffer의 생성자만 바꾸면 된다는 말이다. 

```java
StringBuffer sb;
sb = new StringBuffer();
sb.append("abc");
```

위 코드를 아래와 같이 변경할 수 있다.

```java
StringBuilder sb;
sb = new StringBuilder();
sb.append("abc");
```

StringBuffer도 충분히 성능이 좋기 때문에 성능 향상이 반드시 필요한 경우를 제외하고는 기존에 작성한 코드에서 StringBuffer를 StringBuilder로 굳이 바꿀 필요는 없다.
