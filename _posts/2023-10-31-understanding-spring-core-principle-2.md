---
layout: post
title: 스프링 핵심 원리 이해2 - 객체 지향 원리 적용
description: 스프링 핵심 원리 - 기본편
categories: Spring-SpringBoot
date: 2023-10-31 22:38:00 +0900
---
지난 시간에 작성했던 코드에 좋은 객체 지향 원리들을 적용하는 것을 해볼 것이다.

## 목차

1. 새로운 할인 정책 개발
2. 새로운 할인 정책 적용과 문제점
3. 관심사의 분리
4. AppConfig 리팩터링
5. 새로운 구조와 할인 정책 적용
6. 전체 흐름 정리
7. 좋은 객체 지향 설계의 5가지 원칙의 적용
8. IoC, DI, 그리고 컨테이너
9. 스프링으로 전환하기

기획자가 나타나서 새로운 할인 정책을 추가해 달라고 요구하고 있다. 그래서 새로운 할인 정책을 추가할 것인데, 막상 새로운 정책을 적용해보면 문제가 생긴다. DIP, OCP를 못 지키는 문제가 실제로 발생하는 것이다.

이 문제를 해결하기 위해 여러 과정들을 거치면서 자연스럽게 스프링의 핵심 기능인 스프링 컨테이너가 왜 탄생했는지 알게 된다. (코드로 자연스럽게 이해할 수 있다.)

그리고 제일 마지막에는 실제로 내가 만든 순수한 자바 코드를 스프링 컨테이너에서 동작하도록 굉장히 간단하게 바꿔볼 것이다.

<br>

## 1. 새로운 할인 정책 개발

이제 내가 열심히 개발을 하고 있는데, 악덕 기획자가 서비스 오픈 직전에 와서 "할인 정책이 지금처럼 고정 할인 금액으로 할인하는 것은 별로인 것 같다. 좀 더 합리적인 주문 금액당 퍼센트로 할인해주는 정률 할인으로 바꾸고 싶다."라고 하는 것이다. 예를 들어서 기존 정책은 VIP가 10000원을 주문하든 20000원을 주문하든 항상 1000원을 할인했는데, 이번에 새로 나온 정책은 10%로 지정해두면 고객이 10000원 주문시 1000원을 할인해주고, 20000원 주문시에 2000원을 할인해주는 것이다.

하지만 우리는 유연한 설계가 가능하도록 객체지향 설계 원칙을 준수하면서 개발을 했다. 역할과 구현을 나눠서 개발을 한 것이다.

(참고로, 애자일 소프트웨어 개발 선언에서는 "계획을 따르기보다 변화에 대응하기를"을 주창한다. https://agilemanifesto.org/iso/ko/manifesto.html)

<br>

그렇다면 이전에 내가 짰던 코드가 정말 객체지향 원칙을 잘 준수했는지 확인해보겠다. 이번에는 주문한 금액의 %를 할인해주는 새로운 정률 할인 정책을 추가하자.

<img width="909" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5d78aba2-ee5c-4574-9643-af02e32f1ecd">

간단하다. DiscountPolicy인터페이스가 있고 FixDiscountPolicy가 있었으니까, 그냥 RateDiscountPolicy만 추가로 개발하면 되는 것이다.

<br>

main/java/hello.core/discount패키지 안에 RateDiscountPolicy클래스를 생성한다. <b>RateDiscountPolicy클래스</b>의 코드는 다음과 같다.

```java
package hello.core.discount;

import hello.core.member.Grade;
import hello.core.member.Member;

public class RateDiscountPolicy implements DiscountPolicy {

    private int discountPercent = 10;

    @Override
    public int discount(Member member, int price) {
        if (member.getGrade() == Grade.VIP) {
            return price * discountPercent / 100;
        } else {
            return 0;
        }
    }
}
```

Grade가 VIP이면 ```price * discountPercent / 100```을 리턴하게 한다. 이 로직이 굉장히 의심스러우므로 테스트를 해봐야 한다. 원래는 test쪽에 discount 패키지를 만들고 테스트 클래스를 만들겠지만, IntelliJ에서는 단축키 ```cmd + shift + T```를 누르면 'Create New Test..'가 뜬다. 

<img width="439" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/91643be7-2aab-474b-a3f2-216d080dfa3d">

테스트 라이브러리는 JUnit5를 쓰고, 테스트 클래스는 원래 클래스명 뒤에 Test를 붙여준다. 이것을 그대로 쓰면 된다. 나머지는 손을 안 대도 된다. 그리고 OK를 누른다.

<br>

<b>RateDiscountPolicyTest클래스</b>의 코드는 다음과 같다.

```java
package hello.core.discount;

import hello.core.member.Grade;
import hello.core.member.Member;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;


class RateDiscountPolicyTest {

    RateDiscountPolicy discountPolicy = new RateDiscountPolicy();

    @Test
    @DisplayName("VIP는 10% 할인이 적용되어야 한다.")
    void vip_o() {
        // given
        Member member = new Member(1L, "memberVIP", Grade.VIP);

        // when
        int discount = discountPolicy.discount(member, 10000);

        // then
        Assertions.assertThat(discount).isEqualTo(1000);
    }

    @Test
    @DisplayName("VIP가 아니면 할인이 적용되지 않아야 한다.")
    void vip_x() {
        // given
        Member member = new Member(1L, "memberBASIC", Grade.BASIC);

        // when
        int discount = discountPolicy.discount(member, 10000);

        // then
        Assertions.assertThat(discount).isEqualTo(0);
    }

}
```

우리는 RateDiscountPolicy가 정말 10% 할인이 되는지 볼 것이다. 그래서 discountPolicy를 우선 생성한다. 그리고 vip_o 즉 VIP는 잘 적용되어야 한다는 메서드를 만든다. 그리고 given, when, then으로 구분한다. member을 하나 만들고, discountPolicy에 member을 넘기고 price는 10000원을 넘긴다. 그러면 할인된 금액은 1000원이 나와야 한다. 이것을 검증하기 위해 then 부분에 ```Assertions.assertThat(discount).isEqualTo(1000);```을 입력한다. 이제 실행을 해보면, 다음과 같이 잘 나온다. (JUnit5부터 지원하는 것이다.)

<img width="1153" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/add9fc4c-bef9-4175-90ad-f7fab85c3c6f">

그런데 테스트를 할 때에는 항상 성공 테스트도 중요하지만 실패 테스트도 꼭 만들어봐야 한다. 그래서 아래에 vip_x를 추가한 것이다. 만약 then부분에 ```.isEqualTo(1000)```으로 수정하면 실행했을 때 다음과 같이 오류가 발생한다.

<img width="1349" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0fae58b1-9174-414e-857b-5cec591dccda">

<br>

이제 다시 vip_x의 expected 부분을 0으로 수정하고, 두 개 다 한 번에 실행하면 한 번에 다 잘 돌아간다.

<img width="1152" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4e4c0808-ca03-4c03-84fe-e6fb2a2e0496">

<br>

참고로 Assertions에서는 static import 하는 것이 좋다. Assertions부분을 클릭해두고, ```option + Enter```를 누른 다음, 'Add on-demand static import for ~~'를 클릭하는 것이다. 그러면 다음과 같이 간결하게 변경된다.

```java
package hello.core.discount;

import hello.core.member.Grade;
import hello.core.member.Member;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

import static org.assertj.core.api.Assertions.*;


class RateDiscountPolicyTest {

    RateDiscountPolicy discountPolicy = new RateDiscountPolicy();

    @Test
    @DisplayName("VIP는 10% 할인이 적용되어야 한다.")
    void vip_o() {
        // given
        Member member = new Member(1L, "memberVIP", Grade.VIP);

        // when
        int discount = discountPolicy.discount(member, 10000);

        // then
        assertThat(discount).isEqualTo(1000);
    }

    @Test
    @DisplayName("VIP가 아니면 할인이 적용되지 않아야 한다.")
    void vip_x() {
        // given
        Member member = new Member(1L, "memberBASIC", Grade.BASIC);

        // when
        int discount = discountPolicy.discount(member, 10000);

        // then
        assertThat(discount).isEqualTo(0);
    }

}
```

간결해지면서 위에 ```import static org.assertj.core.api.Assertions.*;```가 올라간다.