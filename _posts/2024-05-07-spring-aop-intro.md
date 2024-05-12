---
layout: post
title: 관점 지향 프로그래밍
categories: Spring-SpringBoot
date: 2024-05-07 17:51:00 +0900
---
이번에는 관점 지향 프로그래밍 (AOP)에 대해서 알아보겠다.

그 전에 앞서 알아본 의존성 주입(Dependency Injection)에 대해서 한 번 정리해보자. Programmer는 Computer가 필요했는데, 고용이 될 때마다 컴퓨터를 가져와서 일을 하는 게 아니라 Spring이라고 하는 업체의 사장님이 Programmer을 고용할 때 이미 가지고 있던 Computer를 집어넣어주는 것이다. 이것이 의존성 주입의 개념이다.

의존성 주입을 하는 데에는 크게 3가지 방법이 있었다. (밑줄 친 것들 세 개)

1. XML 설정 파일 쓰는 법
    1. <u>직접 빈 등록</u>
    2. <u>@Component 스캔</u>
2. <u>Java 설정 파일을 쓰는 법</u>
    1. @Bean 직접 등록
    2. @스캔

<br>

## 관점 지향 프로그래밍

관점 지향 프로그래밍을 하기 전에, 객체지향프로그래밍인 OOP(Object Oriented Programming)에 대하여 "OOP is A PIE" 라는 말이 있다. 각각의 객체지향의 특징을 따고 있는 알파벳의 앞 글자들이다. <b>Abstract(추상화)</b>, <b>Polymorphism(다형성)</b>, <b>Inheritance(상속)</b>, <b>Encapsulation(캡슐화)</b>의 네 가지 특징이다.

또 알면 좋은 것은 객체지향 설계의 5원칙인 SOLID이 있다. 객체지향 프로그래밍은 하나의 프로그래밍 방법론이었다.

그렇다면 관점 지향 프로그래밍은 무엇일까? 관점 지향 프로그래밍은 OOP를 보조하기 위해 나온 개념인데, 이것 또한 프로그래밍 방법론이다.

'관점'에 focusing을 맞춰서 프로그래밍을 하는 것이다. 그렇기에 AOP는 하나의 옵션 정도로 생각할 수 있다. (이것을 모른다고 해서 Spring을 못 하는 것은 아니라는 것이다.)

<br>

### AOP (Aspect Oriented Programming)

어플리케이션 로직에는 핵심 기능과 부가 기능이 있다.

* 핵심 기능 : 객체가 제공하는 고유의 기능
* 부가 기능 : 핵심 기능을 보조하기 위한 기능 (시간 측정, 로그 추적, 트랜잭션 관리 등)

은행을 예로 들었을 때, 핵심 기능으로는 예금 인출, 예금 송금 등이 있겠다. 예금 인출을 하기 전에는 로그 추적, 보안 모듈 등의 기능이 필요하다. 보안 모듈의 경우 예금 인출을 하기 전에, 내가 맞는지 확인하기 위해 비밀번호를 입력하는 것이다. 그리고 예금 인출이 정상적으로 종료 되었다면 잔액 확인 명세표 출력 같은 기능이 있을 수 있다. 또 중간에 잔고가 100만원 밖에 없는데 200만원을 꺼내려 한다면 '돈 부족 Exception' 같은 것이 뜰 것이다. 또 마지막에 해야 하는 것은 카드를 돌려주는 것이다.

이처럼 하나의 기능을 만들기 위해서는 각각 필요한 기능들이 매우 많다. 원래는 이것을 통으로 작성을 했다면, 이제는 '로그 추적', '보안 모듈' 과 같은 것들을 부가 기능으로 생각하는 것이다. 핵심 기능을 보조하기 위한 기능들인 것이다.

핵심 기능이라는 객체 안에서 부가 기능을 작성했다. '예금 인출'이라는 핵심 기능과 '예금 송금'이라는 핵심 기능 안에는 '로그 추적', '보안 모듈'과 같은 부가 기능들이 공통적으로 존재한다. 그런데 이러한 부가 기능을 가지고 있는 핵심 기능들의 수가 매우 많아지면, 부가 기능들을 재활용하는 것이 효율적이다.

어떻게 재활용을 할 수 있을까? 메인 기능 실행 전, 실행 후, 예외, 그냥 종료되었을 때 등... <b>관점을 나눠서 해결할 수 있다.</b>

관점에 따라 나눌 수 있다는 것을 '<b>Cross Cutting Concern(공통 관심사항)</b>'이라고 한다. Core Concern(핵심 관심사항)들에 공통적으로 있는 부가 기능들을 쭉 잘라서 공통 관심사항이라고 하는 것이다.

- <b>Cross Cutting Concern (공통 관심사항)</b>
- <b>Core Concern (핵심 관심사항)</b>

<br>

OOP에서 모듈화의 핵심 단위는 클래스인 반면, <b>AOP에서 모듈화의 단위는 Aspect 이다.</b>

Aspect는 여러 타입과 객체에 거쳐서 사용되는 기능 (Cross-Cutting, 트랜잭션 관리 같은 부가기능)을 모듈화시킨 것이다. AOP는 OOP를 대체하는 것이 아니라, 보조하는 것이 목적이다. (AOP는 '관점'이 주요 포인트)

<br>

## AOP 용어

<table>
    <tr>
        <th style="text-align: center;">용어</th>
        <th style="text-align: center;">내용</th>
    </tr>
    <tr>
        <td style="text-align: center;">Target</td>
        <td>* 핵심기능을 담고 있는 객체 → 부가기능을 부여할 대상</td>
    </tr>
    <tr>
        <td style="text-align: center;">Aspect</td>
        <td>* 여러 클래스에 공통적으로 적용되는 공통 관심 사항 (AOP의 기본 모듈)<br>* Advice + Point Cut</td>
    </tr>
    <tr>
        <td style="text-align: center;">Join Point</td>
        <td>* Advice가 적용될 수 있는 위치 (메서드 실행, 생성자 호출 등)<br>* Spring AOP에서는 메서드 실행 지점으로 제한</td>
    </tr>
    <tr>
        <td style="text-align: center;">Point Cut</td>
        <td>* Join Point 중에서 Advice를 적용하기 위한 조건 서술<br>* Aspect는 지정한 Point Cut에 일치하는 모든 Join Point에서 실행</td>
    </tr>
    <tr>
        <td style="text-align: center;">Advice</td>
        <td>* 부가 기능, 특정 Join Point에서 취해지는 행동<br>* Around, Before, After 등의 타입이 존재</td>
    </tr>
    <tr>
        <td style="text-align: center;">Weaving</td>
        <td>* Point Cut으로 결정한 Target의 Join Point에 Advice를 적용하는 것<br>* 컴파일 시점, 클래스 로딩 시점, 런타입 시점에서 수행 가능하나 Spring AOP는 런타임에 수행</td>
    </tr>
    <tr>
        <td style="text-align: center;">AOP Proxy</td>
        <td>* AOP를 구현하기 위해 AOP Framework에 의해 생성된 객체<br>* Spring AOP는 JDK dynamic proxy 또는 CGLIB proxy 사용</td>
    </tr>
</table>

Target의 경우, 내가 예금 인출 기능을 가지고 있는데 여기에는 로그 추적, 예외 처리 등의 기능이 들어있으면 좋겠다. 그런데 이 구분을 명확하게 나눠서, 큰 덩어리에는 "예금 인출"이라는 기능만 만들어놓은 것이다. 이 Target에다가 "로그 추적", "예외 처리"와 같은 부가 기능을 붙이는 것이다. 즉, 큰 덩어리를 Target이라고 부른다.

Aspect는 AOP의 기본 모듈이다. 즉 위 예시에서 "로그 추적", "예외 처리" 등과 같이 공통 관심 사항을 묶어 놓은 것을 Aspect라고 하는 것이다.

Join Point의 경우, "예금 인출" 객체 안에서도 메서드들이 있을 것이다. 예금이라고 하는 커다란 클래스 안에 인출 메서드, 송금 메서드 등이 있을 것이다. Join Point라는 것은 "로그 추적", "예외 처리" 각각이 들어갈 수 있는 위치를 말한다. 가령 "예금 인출이라고 하는 메서드 실행 전에 로그 추적을 할 수 있다"의 방식으로 들어가는 것이다. 즉, Join을 해서 들어갈 수 있는 Point이다. (부가 기능이 실행될 수 있는 곳들. 들어갈 수 있는 위치)

Point Cut의 경우, 이러한 Join Point 들 중에서 모든 곳에 다 적용을 할 것이냐? 하면 그것은 아닌 것이다. 가령 "잔액 부족 Excpetion"은 인출과 송금 두 개에만 들어가면 된다. 또 입금에는 "잔액 부족 Exception" 처리를 할 필요가 없다. 그래서 "잔액 부족 Exception"은 인출과 송금이라는 메서드에만 동작을 시키는 것이다. 이렇게 Point를 정해주는 방식이 Point Cut이다. Join Point 중에서 Advice를 적용하기 위한 조건을 서술한다. 이것을 작성할 때에는 Execution이라는 것을 활용한다. 가령 "로그 추적"의 Point Cut을 작성해서 ~~ 한 곳에서 작동하도록 하는 것이다.

Advice는 부가 기능이다. 전반적으로를 뜻하는 Around, 하기 전인 Before, 한 후인 After 등의 타입이 존재한다. 결국에는 <b>Advice와 Point Cut을 합치면 이것이 바로 Aspect이다.</b>

Weaving의 경우, 어디에 들어갈 지(Point Cut)을 정해놓고 Join Point에 Advice를 적용하는 행위 자체를 말한다. 이 Weaving은 컴파일 시점, 클래스 로딩 시점, 런타임 시점의 세 개 시점에서 실행이 가능하나, Spring AOP에서는 런타임 시 실행을 한다. 즉, 실행되는 와중에 이렇게 Weaving 즉, 짜맞추기가 이뤄지는 것이다.

AOP Proxy의 경우, 그냥 '프록시를 쓴다더라'라고 알면 되겠다.

(이 용어들은 알고 있어야 한다.)