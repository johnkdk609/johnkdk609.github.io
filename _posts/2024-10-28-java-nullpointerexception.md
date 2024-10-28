---
layout: post
title: Java NullPointerException
categories: Java
date: 2024-10-28 22:34:00 +0900
---
자바(Java)를 다루다 보면 자주 마주치는 것이 NullPointerException 이다. NullPointerException 은 null 때문에 발생하는 Runtime Exception 이다.

이름 그대로 null 을 가리키다(Pointer)인데, 이때 발생하는 예외(Exception)이다.

'null' 은 '없다'는 뜻이므로 결국 주소가 없는 곳을 찾아갈 때 발생하는 예외이다. 객체를 참조할 때에는 ```.``` 을 사용한다. 이렇게 하면 참조값을 사용해서 해당 객체를 찾아갈 수 있는데, 참조값이 ```null``` 이라면 값이 없으므로 찾아갈 수 있는 객체(인스턴스)가 없다.

즉, NullPointerException 은 ```null``` 에 ```.``` 을 찍어서 접근하려 할 때 발생한다.

<br>

자바를 다루다가 NullPointerException 을 마주치는 경우를 나열해 보겠다.