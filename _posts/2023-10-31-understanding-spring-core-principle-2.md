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


## 2. 새로운 할인 정책 적용과 문제점

이제 새로운 할인 정책의 적용과 문제점에 대해 알아보겠다. 무언가 적용하려는데 문제가 생기는 것이다.

할인 정책을 바로 적용하려면 OrderServiceImpl로 들어가야 한다. 적용한 <b>OrderServiceImpl클래스</b> 코드는 다음과 같다.

```java
package hello.core.order;

import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPolicy;
import hello.core.discount.RateDiscountPolicy;
import hello.core.member.Member;
import hello.core.member.MemberRepository;
import hello.core.member.MemoryMemberRepository;

public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository = new MemoryMemberRepository();
//    private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
    private final DiscountPolicy discountPolicy = new RateDiscountPolicy();

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        int discountPrice = discountPolicy.discount(member, itemPrice);

        return new Order(memberId, itemName, itemPrice, discountPrice);
    }
}
```

여기서 할인 정책(discountPolicy)이 고정 할인 금액(FixDiscountPolicy)로 되어 있었다. 얘를 위와 같이 RateDiscountPolicy로 바꾸면 끝난다.

<br>

할인 정책을 변경하려면 클라이언트인 ```OrderServiceImpl```의 코드를 고쳐야 한다. 원래 FixDiscountPolicy로 되어 있는데 RateDiscountPolicy 객체로 바꾸려면 ```new ~~```를 실제로 고쳐야 한 것이다.

### 문제점 발견

* 우리는 역할과 구현을 충실하게 분리했다. → OK
* 다형성도 활용하고, 인터페이스와 구현 객체를 분리했다. → OK
* OCP, DIP 같은 객체지향 설계 원칙을 충실히 준수했다. → 그렇게 보이지만 사실은 아니다.
* DIP: 주문서비스 클라이언트(OrderServiceImpl)는 DiscountPolicy인터페이스에 의존하면서 DIP를 지킨 것 같은데?
    * → 클래스 의존관계를 분석해 보자. 추상(인터페이스)뿐만 아니라 <b>'구체(구현) 클래스에도 의존'</b>하고 있다.
        * 추상(인터페이스) 의존: DiscountPolicy
        * 구체(구현) 클래스: FixDiscountPolicy, RateDiscountPolicy
* OCP: 변경하지 않고 확장할 수 있다고 했는데!
    * → <b>지금 코드는 기능을 확장해서 변경하면, 클라이언트 코드에 영향을 준다. 따라서 OCP를 위반</b>한다.

<br>

왜 클라이언트 코드를 변경해야 했는지, 클래스 다이어그램으로 의존관계를 분석해보자.

기대했던 의존관계는 다음과 같다.

<img width="905" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/091f0fee-2d10-42b2-9956-7f37d8961e09">

OrderServiceImpl은 분명히 DiscountPolicy에 의존하고 있다. 지금까지 단순히 DiscountPolicy인터페이스에 의존한다고 생각했다.

그런데 실제 의존관계는 다음과 같다.

<img width="904" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/83e53ea9-50ef-4df5-84f1-d0a777d67e62">

잘 보면 클라이언트인 OrderServiceImpl이 DiscountPolicy인터페이스뿐만 아니라 FixDiscountPolicy인 구체 클래스도 함께 의존하고 있다. 실제 코드를 보면 의존하고 있는 것이고, 이는 <b>DIP를 위반한 것</b>이다.

위에 OrderServiceImpl 실제 코드를 보면, 다음과 같이 FixDiscountPolicy, RateDiscountPolicy에 실제로 의존하고 있다.

<img width="646" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/6e196a28-2f48-44fa-b4a9-92a4f7af568d">

<br>

DIP를 위반했기 때문에 할인 정책을 FixDiscountPolicy에서 RateDiscountPolicy로 바꾸는 순간 OrderServiceImpl의 소스코드를 함께 변경할 수밖에 없다.

<img width="911" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1b33c2d4-cacc-4642-be1c-3f2155e502d7">

이게 바로 의존성이다. 의존관계가 있으면 어떤 것을 건드는 순간 코드를 바꿔야 한다.

설계가 잘 되어 있어서 변경이 작았기는 하다. 아무튼 FixDiscountPolicy를 RateDiscountPolicy로 변경하는 순간 OrderServiceImpl의 소스코드도 함께 변경해야 한다. 즉, <b>OCP를 위반한 것</b>이다.

<br>

운전을 생각해봤을 때, 역할과 구현을 분리하면 예를 들어 내가 내연기관 차를 타다가 전기차로 바꿔도 운전 면허증이 있으면 그냥 차를 운전할 수 있다. 그런데 지금 이거는 차를 새로 바꿨다고 해서 내가 라이선스를 갱신해야 하는 상황이 된 것이다. 뭔가 안 맞는 것이다.

<br>

### 어떻게 문제를 해결할 수 있을까?

* 클라이언트 코드인 OrderServiceImpl은 DiscountPolicy의 인터페이스 뿐만 아니라 구체 클래스도 함께 의존한다.
* 그래서 구체 클래스를 변경할 때 클라이언트 코드도 함께 변경해야 하는 문제가 있었다.
* <u>DIP 위반 → 추상에만 의존하도록 변경(인터페이스에만 의존)</u>
* DIP를 위반하지 않도록 인터페이스에만 의존하도록 의존관계를 변경하면 된다.

즉, 인터페이스에만 의존하도록 설계를 변경하면 된다.

<img width="906" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/9fd263aa-4bdc-4b8c-bd3c-b8c5224db028">

OrderServiceImpl이 DiscountPolicy에만 의존하도록 변경하면 된다.

<br>

OrderServiceImpl의 코드를 다음과 같이 변경하면 된다.

```java
package hello.core.order;

import hello.core.discount.DiscountPolicy;
import hello.core.member.Member;
import hello.core.member.MemberRepository;
import hello.core.member.MemoryMemberRepository;

public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository = new MemoryMemberRepository();
    private DiscountPolicy discountPolicy;

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        int discountPrice = discountPolicy.discount(member, itemPrice);

        return new Order(memberId, itemName, itemPrice, discountPrice);
    }
}
```

final은 무조건 값이 할당되어야 하므로 지운다. 일단은 DiscountPolicy에 한정해서만 보겠다. 이제 인터페이스 즉 추상에만 의존하고 있다.

<br>

그런데 이 코드를 실행하면 다음과 같이 NPE(NullPointerException)가 터진다. 이전에 만들었던 OrderServiceTest를 실행한 결과이다.

<img width="1295" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c119a869-c382-46de-bdd1-0f6604a1d429">

Null에 점 찍으면 NullPointerException이 발생한다.

createOrder를 호출했을 때, discountPolicy가 아무 값도 할당되어 있지 않기 때문에 Null에 점 찍으니 이런 예외가 발생한 것이다.

<br>

구현체가 없는데 어떻게 코드를 실행할 수 있을까?

<b>해결방안</b>: 이 문제를 해결하려면 누군가가 클라이언트인 OrderServiceImpl에 DiscountPolicy의 구현 객체를 대신 생성하고 주입해주어야 한다.


## 3. 관심사의 분리

애플리케이션을 하나의 공연이라 생각해보자. 각각의 인터페이스를 배역(배우 역할)이라 생각하자. 그런데, 실제 배역에 맞는 배우를 선택하는 것은 누가 하는가?

로미오와 줄리엣 공연을 하면 로미오 역할은 누가 할지, 줄리엣 역할을 누가 할지는 배우들이 정하는 것이 아니다. 이전 코드는 마치 로미오 역할(인터페이스)을 하는 레오나르도 디카프리오(구현체, 배우)가 줄리엣 역할(인터페이스)을 하는 여자 주인공(구현체, 배우)을 직접 초빙하는 것과 같다. 디카프리오는 공연도 해야하고 동시에 여자 주인공도 공연에 직접 초빙해야 하는 <b>다양한 책임</b>을 가지고 있다.

코드를 보면 이해하기 쉽다. 기존의 OrderServiceImpl에서는 다음과 같이 적혀 있었다.

```java
private DiscountPolicy discountPolicy = new FixDiscountPolicy();
```

OrderServiceImpl은 OrderService와 관련된 로직만 해야 하는데, 여기서는 '나는 FixDiscountPolicy를 해야 돼'라며 자기가 직접 선택하는 것이다. 굉장히 부차적인 것까지 OrderServiceImpl이 직접 객체를 생성하고 선택해서 할당하는 것이다. 마치 배우가 직접 여자 주인공(ex. 올리비아 핫세)을 초빙하는 것과 마찬가지인 것이다.

### 관심사를 분리해야 한다

배우는 본인의 역할인 배역을 수행하는 것에만 집중해야 한다. 디카프리오는 어떤 여자 주인공이 선택되더라도 똑같이 공연할 수 있어야 한다.

* 공연을 구성하고, 담당 배우를 섭외하고, 역할에 맞는 배우를 지정하는 책임을 담당하는 별도의 <b>공연 기획자</b>가 나올 시점이다.

공연 기획자를 만들고, 배우와 공연 기획자의 책임을 확실히 분리해야 한다. 애플리케이션은 이렇게 개발을 해야 한다.


### AppConfig의 등장

애플리케이션의 전체 동작 방식을 구성(config)하기 위해, <u>구현 객체를 생성하고 연결하는 책임을 가지는 별도의 설정 클래스를 만들 것</u>이다.

애플리케이션 전반에 대한 구성을 책임지는 것이라 보면 된다. main/java/hello.core 바로 아래에 AppConfig클래스를 생성한다. <b>AppConfig클래스</b>의 코드는 다음과 같다.

```java
package hello.core;

import hello.core.discount.FixDiscountPolicy;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;

public class AppConfig {

    public MemberService memberService() {
        return new MemberServiceImpl(new MemoryMemberRepository());
    }

    public OrderService orderService() {
        return new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
    }
}
```

먼저, memberService와 orderService를 만든다. 이전에는 객체를 생성하고 인터페이스에 어떤 것을 할당할지 MemberService 안에서 직접 했었다.

```java
package hello.core.member;

public class  MemberServiceImpl implements MemberService {

    private final MemberRepository memberRepository = new MemoryMemberRepository();

    @Override
    public void join(Member member) {
        memberRepository.save(member);
    }

    @Override
    public Member findMember(Long memberId) {
        return memberRepository.findById(memberId);
    }
}
```

MemberServiceImpl을 보면 MemberService 오른쪽에서 ```new MemoryMemberRepository();```를 하여 MemberServiceImpl이 직접 지정해준 것이다. 이제 이런 것을 AppConfig에서 다 할 것이다. 일단, MemberServiceImpl의 ```new MemoryMemberRepository``` 부분을 지우고, 생성자를 만든다.

```java
package hello.core.member;

public class  MemberServiceImpl implements MemberService {

    private final MemberRepository memberRepository;

    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    @Override
    public void join(Member member) {
        memberRepository.save(member);
    }

    @Override
    public Member findMember(Long memberId) {
        return memberRepository.findById(memberId);
    }
}
```

그리고, 이 생성자를 통해서 MemberRepository에 구현체가 무엇이 들어갈지 선택한다. AppConfig클래스에서 ```return new MemberServiceImpl(new MemoryMemberRepository());```를 입력하는 것이다. AppConfig를 통해서 MemberService를 불러다 쓰게 된다. MemberService 구현체인 객체가 생성이 되는데, 그때 이게 중요하다. 생성자에 들어가보기 위해 MemberServiceImpl 부분에 마우스 커서를 두고, Alt 버튼을 누르고 들어가본다.

MemberServiceImpl클래스의 코드를 봤을 때, ```public MemberServiceImpl(MemberRepository memberRepository)```의 memberRepository에 MemoryMemberRepository가 들어와서 ```private final MemberRepository memberRepository;```의 memberRepository에 할당이 된다. 지금 보면, MemberServiceImpl에 MemoryMemberRepository에 대한 코드가 없다. 오로지 MemberRepository라는 인터페이스만 있다. 얘는 이제 추상화에만 의존하는 것이다. DIP를 지키는 것이다. 구체적인 것에 대해서는 전혀 모른다. 이것은 밖에서 생성을 해서 넣어주는 것이다. 이것을 생성자를 통해서 객체가 들어간다고 해서 "생성자 주입"이라고 한다.

다시 AppConfig클래스로 돌아와서, OrderService를 만들 것이다. 이번에도 생성자 주입을 해줄 것이다. OrderServiceImpl에 들어가보면, OrderService는 사용하는 필드가 두 개이다. 기존 <b>OrderServiceImpl클래스</b>의 코드는 다음과 같았다.

```java
package hello.core.order;

import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPolicy;
import hello.core.member.Member;
import hello.core.member.MemberRepository;
import hello.core.member.MemoryMemberRepository;

public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository = new MemoryMemberRepository();
    private final DiscountPolicy discountPolicy = new FixDiscountPolicy();

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        int discountPrice = discountPolicy.discount(member, itemPrice);

        return new Order(memberId, itemName, itemPrice, discountPrice);
    }
}
```

MemberRepository도 필요하고 DiscountPolicy도 필요한 상황이다. 그러면, 뒷부분을 과감하게 지우고, 생성자를 추가한다. (final이 있으면 기본으로 할당을 하든 생성자를 통해서 할당이 되어야 한다.)

<b>OrderServiceImpl클래스</b>의 코드를 다음과 같이 수정하는 것이다.

```java
package hello.core.order;

import hello.core.discount.DiscountPolicy;
import hello.core.member.Member;
import hello.core.member.MemberRepository;

public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        int discountPrice = discountPolicy.discount(member, itemPrice);

        return new Order(memberId, itemName, itemPrice, discountPrice);
    }
}
```

두 개를 다 받아야 하므로, AppConfig클래스에서 다음과 같이 두 개의 필드를 넣는 것이다.

```java
return new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
```

이제 AppConfig를 통해서 누군가 orderService를 조회하면 얘는 OrderServiceImpl이 반환되는데, 거기에 MemoryMemberRepository와 FixDiscountPolicy가 들어간다. 그러면 OrderServiceImpl에서 생성자를 통해서 MemoryMemberRepository와 FixDiscountPolicy가 넘어가서 값이 할당된다.

OrderServiceImpl을 보면 DIP를 철저하게 지키고 있다. 인터페이스에만 의존하고 있고, 구체적인 클래스에 대해서 전혀 모르는 것이다. 누군가가 MemoryMemberRepository를 넣어줄지, DbMemberRepository를 넣어줄지, JdbcMemberRepository를 넣어줄지 모르는 것이다. DiscountPolicy가 OrderServiceImpl의 입장에서는 FixDiscountPolicy가 들어올지, RateDiscountPolicy가 들어올지 전혀 모르는 것이다. 얘는 마치 대본 보고 자기 공연하듯이 이 로직만 실행하면 되는 것이다.