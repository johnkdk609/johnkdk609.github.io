---
layout: post
title: final
categories: Java
date: 2024-01-26 00:30 +0900
---
자바 final에 대해 알아보겠다.

final의 특징들은 다음과 같다.

- 해당 선언이 최종 상태, 결코 수정될 수 없음.
- <b>final 클래스: 상속 금지</b>
- <b>final 메서드: overriding 금지</b>
- <b>final 변수: 더 이상 값을 바꿀 수 없음. (상수화)</b>

<br>

가령, 다음 코드를 보자.

```java
public class FinalExample {

    public final int finalNum = 100;
    // finalNum = 200;  // final로 상수화했기 때문에 값 변경 불가능

    public final void finalMethod() {
        System.out.println("final 메서드 예시입니다.");
    }
}
```

```java
public class FinalTest extends FinalExample {

    // @Override
    // public void finalMethod() {
    //     System.out.println("상속받은 final 메서드 재정의가 가능할까요?");
    // }
}
```

위 코드를 봤을 때, FinalExample클래스에서 finalNum 변수는 final이 선언되었다. 그렇기 때문에 아랫줄에서 finalNum에 새로운 값을 할당하려고 하면 오류가 발생한다.

FinalTest클래스는 FinalExample클래스를 상속받았다. 이때, FinalExample클래스에서 finalMethod() 메서드는 final이 선언되었기 때문에, FinalTest클래스에서는 finalMethod()를 오버라이드할 수 없다.

<img width="807" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/665f9e1b-1a21-47c3-86c2-14b7e4dbe591">

<br>

만약, FinalExample클래스에 final이 붙어있었다면, ```FinalTest extends FinalExample``` 자체가 불가능해질 것이다. final 클래스는 상속이 금지되기 때문이다.