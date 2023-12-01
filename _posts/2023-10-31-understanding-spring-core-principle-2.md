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

<br>

AppConfig는 애플리케이션의 실제 동작에 필요한 <b>구현 객체를 생성</b>한다.

* MemberServiceImpl
* MemoryMemberRepository
* OrderServiceImpl
* FixDiscountPolicy

AppConfig는 생성한 객체 인스턴스의 참조(레퍼런스)를 <b>생성자를 통해서 주입(연결)</b>해준다.

* MemberServiceImpl → MemoryMemberRepository
* OrderServiceImpl → MemoryMemberRepository, FixDiscountPolicy

가령, MemberServiceImpl의 경우, AppConfig에서 다음과 같이 ```new MemoryMemberRepository```를 해서 객체를 생성하고 이것에 대한 참조값을 MemberServiceImpl에 넣어주는 것이다.

<img width="589" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/9bebf0a1-d0d5-42fd-b2f5-079edd0c8218">

그래서 MemberServiceImpl에는 MemoryMemberRepository가 들어가고, OrderServiceImpl에는 MemoryMemberRepository와 FixDiscountPolicy 두 가지가 파라미터로 들어가는데, 이것을 '생성자 주입'이라고 한다.

(참고: 각 클래스에 생성자가 없으면 컴파일 오류가 발생한다.)

<br>

결과적으로, 설계 변경으로 MemberServiceImpl은 MemoryMemberRepository를 의존하지 않는다. DIP를 지키고 있는 것이다. 단지 MemberRepository인터페이스만 의존한다.

MemberServiceImpl 입장에서 생성자를 통해 어떤 구현 객체가 들어올지(주입될지)는 알 수 없다. 다형성에 의해서 무언가 들어오는 것이다.

MemberServiceImpl의 생성자를 통해서 어떤 구현 객체를 주입할지는 오직 외부(AppConfig)에서 결정된다. (예전에는 MemberServiceImpl 안에서 결정되었었다.)

MemberServiceImpl은 이제부터 <b>의존관계에 대한 고민은 외부</b>에 맡기고 <b>실행에만 집중하면 된다.

<br>

이제 클래스 다이어그램을 보겠다.

<img width="905" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/832a1d8c-dd7b-4c9a-b88c-4d5934e0b780">

먼저 MemberService인터페이스를 구현하는 것이 MemberServiceImpl이다. 그리고 이 MemberServiceImpl은 MemberRepository인터페이스를 의존한다. 여기까지는 이전 그림과 똑같다. 그런데 차이가 나는 것이 AppConfig가 등장한다. AppConfig가 MemberServiceImpl이라는 객체를 생성한다. 또, AppConfig가 무엇을 생성하냐면, 원래 MemberServiceImpl이 생성했던 MemoryMemberRepository를 AppConfig가 직접 생성한다. 그렇게 해서 객체의 생성과 연결을 AppConfig가 담당하는 것이다.

* 객체의 생성과 연결은 AppConfig가 담당한다.
* <b>DIP 완성</b>: MemberServiceImpl은 MemberRepository인 추상에만 의존하면 된다. 이제 구체 클래스를 몰라도 된다.
* <b>관심사의 분리</b>: 객체를 생성하고 연결하는 역할과 실행하는 역할이 명확히 분리되었다.

<br>

이것을 조금 더 풀어서 설명해보겠다. 다음은 회원 객체 인스턴스 다이어그램이다.

<img width="972" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ac3123db-7c15-4bbe-a61f-4ab9f2eb5b3e">

appConfig가 memoryMemberRepository객체를 생성했다. 

<img width="614" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/6567eaee-cab0-431a-b9a6-7a4310f480ba">

MemberService 객체를 만들 때 MemoryMemberRepository를 생성하는 것이다.

그 다음에 memberServiceImpl을 생성할 때, memoryMemberRepository의 참조값 ```x001```을 생성자에 같이 넘긴다. 그래서 memberServiceImpl은 생성한 memoryMemberRepository에 대한 값을 주입받게 되는 것이다.

* appConfig객체는 memoryMemberRepository객체를 생성하고 그 참조값을 memberServiceImpl을 생성하면서 생성자로 전달한다.
* 클라이언트인 memberServiceImpl 입장에서 보면 의존관계를 마치 외부에서 주입해주는 것 같다고 해서 <b>DI(Dependency Injection)</b>, 우리 말로 <b>의존관계 주입 또는 의존성 주입</b>이라고 한다.

<br>

이제 OrderServiceImpl에 대해 알아보겠다.

설계 변경으로 OrderServiceImpl은 이제 더 이상 FixDiscountPolicy를 의존하지 않는다. 순수한 인터페이스에만 의존하고, <u>DIP를 만족</u>하는 것이다. 단지 DiscountPolicy인터페이스에만 의존한다. 

OrderServiceImpl 입장에서 생성자를 통해 어떤 구현 객체가 들어올지(주입될지)는 알 수 없다. 마치 배우가 상대 배우가 누가 캐스팅될지 알 수 없는 것이다.

OrderServiceImpl의 생성자를 통해서 어떤 구현 객체를 주입할지는 오직 외부(AppConfig)에서 결정한다. OrderServiceImpl은 이제 실행에만 집중하면 된다. 

결과적으로 OrderServiceImpl에는 MemoryMemberRepository, FixDiscountPolicy 객체의 의존관계가 주입된다.

<br>

이제 한 번 실행해보겠다. 이전에 만들었던 MemberApp을 보면 다음과 같이 코드에 빨간줄이 나 있는 것을 볼 수 있다.

<img width="659" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/293cfe84-3ef1-4442-8bc3-bec5bb4f9510">

MemberServiceImpl에 마우스를 갖다대고 ```Alt```를 누르고 들어간 다음, MemberServiceImpl에서 '4 related problems'를 클릭하면 다음과 같이 어떤 문제들이 있는지 콘솔에 나타난다. (IntelliJ의 기능이다.)

<img width="1686" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2a2d7213-801a-4360-9455-22b3c25541fd">

기존에는 MemberApp에서 직접 MemberService 객체도 생성하고 했는데, 이제 그게 아니라 AppConfig를 이용해서 애플리케이션을 개발해볼 것이다.

수정한 MemberApp클래스의 코드는 다음과 같다.

```java
package hello.core;

import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;

public class MemberApp {

    public static void main(String[] args) {
        AppConfig appConfig = new AppConfig();
        MemberService memberService = appConfig.memberService();
//        MemberService memberService = new MemberServiceImpl();
        Member member = new Member(1L, "memberA", Grade.VIP);
        memberService.join(member);

        Member findMember = memberService.findMember(1L);
        System.out.println("new member = " + member.getName());
        System.out.println("find Member = " + findMember.getName());
    }
}
```

AppConfig를 무조건 만들어야 한다. 그리고 ```appConfig.memberService();```가 필요하다. 기존에는 MemberServiceImpl을 직접 메인 메서드에서 생성해줬다. 그러면 MemberServiceImpl에서 또 MemoryMemberRepository를 생성했다. 마치 순차적으로 돌듯이. 그런데 이것은 AppConfig에서 다 결정한다. 이제 AppConfig에서 MemberService 달라고 하면 memberService인터페이스를 주고, 이 memberServiceImpl이 들어가 있는 이 memberService인터페이스를 받는다.

```appConfg.memberService();```에서 memberService 부분을 타고 들어가보면, 다음과 같이 new MemberServiceImpl객체를 생성하면서 '나는 MemberServiceImpl인데 나는 MemoryMemberRepository를 쓸 거야'라는 것을 딱 해서 주입해준다.

<img width="565" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/bfd21486-379f-4f39-be63-bb171f720543">

<br>

이제 MemberApp을 실행해보면, 다음과 같이 에러가 발생한다.

<img width="1432" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f775796c-9d72-418d-9ea8-05d70f05b479">

OrderApp 쪽에서 컴파일 오류가 발생했는데, 이럴 때에는 잠시 null로 설정해두면 된다.

<img width="831" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/658586f1-b109-4a4f-82a7-f3d7e4bc4dc2">

이제 다시 MemberApp을 실행하면 다음과 같이 성공적으로 된다.

<img width="791" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/974ec3e5-e270-4918-92de-0a046fbfb19e">

<br>

이번에는 OrderApp으로 넘어가서, 얘도 AppConfig를 쓰도록 바꾼다. 수정한 <b>OrderApp클래스</b>의 코드는 다음과 같다.

```java
package hello.core;

import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import hello.core.order.Order;
import hello.core.order.OrderService;

public class OrderApp {

    public static void main(String[] args) {

        AppConfig appConfig = new AppConfig();
        MemberService memberService = appConfig.memberService();
        OrderService orderService = appConfig.orderService();

//        MemberService memberService = new MemberServiceImpl();
//        OrderService orderService = new OrderServiceImpl();

        Long memberId = 1L;
        Member member = new Member(memberId, "memberA", Grade.VIP);
        memberService.join(member);

        Order order = orderService.createOrder(memberId, "itemA", 10000);

        System.out.println("order = " + order);
        System.out.println("order.calculatePrice = " + order.calculatePrice());
    }
}
```

appConfig에서 memberService가 필요하면 꺼내고, appConfig에서 orderService가 필요하면 꺼내는 것이다. 

memberService는 이미 봤으니, orderService를 타고 들어가면 다음과 같다.

<img width="743" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/606084bd-2b54-427d-8967-dd56eb766b3a">

OrderServiceImpl을 반환하는데, 생성자로 MemoryMemberRepository와 FixDiscountPolicy 두 개가 있는 것이다. 그렇게 해서 OrderServiceImpl이 MemoryMemberRepository와 FixDiscountPolicy객체를 참조하도록 그림을 완성시키고, 완성된 OrderServiceImpl객체를 반환하는 것이다.

OrderApp을 실행해보면, 다음과 같이 성공적으로 된다.

<img width="860" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ca5304eb-8148-4e25-b39b-5173852d79ee">

결과를 보면 똑같다. 이제 OrderApp도 이제 더 이상 구체 클래스에 의존할 필요가 없다. 인터페이스에만 의존하고 있다.

<br>

그리고, 테스트코드의 오류도 수정해줘야 한다.

먼저, Member부터 고쳐보겠다. MemberServiceTest클래스에서 생성자를 넣어줘야 한다. 이것도 AppConfig를 사용하도록 바꿔줘야 한다.

다음과 같이 수정한다.

```java
package hello.core.member;

import hello.core.AppConfig;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

public class MemberServiceTest {

    MemberService memberService;

    @BeforeEach
    public void beforeEach() {
        AppConfig appConfig = new AppConfig();
        memberService = appConfig.memberService();
    }

    @Test
    void join() {
        // given
        Member member = new Member(1L, "memberA", Grade.VIP);

        // when
        memberService.join(member);
        Member findMember = memberService.findMember(1L);

        // then
        Assertions.assertThat(member).isEqualTo(findMember);

    }
}
```

여기서 ```@BeforeEach```는 테스트를 실행하기 전에 무조건 실행시키는 것이다. 여기서 appConfig를 만들고, ```memberService = appConfig.memberService();```를 통해 memberService를 할당해준다. 테스트가 2개 있으면 두 번 돈다.

<br>

또, OrderServiceTest클래스도 수정해준다.

OrderServiceTest는 다음과 같이 수정한다.

```java
package hello.core.order;

import hello.core.AppConfig;
import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

public class OrderServiceTest {

    MemberService memberService;
    OrderService orderService;

    @BeforeEach
    public void beforeEach() {
        AppConfig appConfig = new AppConfig();
        memberService = appConfig.memberService();
        orderService = appConfig.orderService();
    }

    @Test
    void createOrder() {
        Long memberId = 1L;
        Member member = new Member(memberId, "memberA", Grade.VIP);
        memberService.join(member);

        Order order = orderService.createOrder(memberId, "itemA", 10000);
        Assertions.assertThat(order.getDiscountPrice()).isEqualTo(1000);
    }
}
```

(```cmd + E + Enter``` 단축키를 통해 직전에 보던 클래스로 이동할 수 있다.)

마찬가지로 OrderServiceTest에도 ```@BeforeEach```를 추가한다.

<br>

이제 MemberServiceTest에서 테스트를 다 돌려보면, 성공적으로 돌아간다.

<img width="1152" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/78d509eb-5ef0-4466-a242-959ea6e8be12">

<br>

이제 DIP를 지키게 된 것이다. 

### 정리

* AppConfig를 통해서 관심사를 확실하게 분리했다.
* 배역, 배우를 생각해보자.
* AppConfig는 공연 기획자다.
* AppConfig는 구체 클래스를 선택한다. 배역에 맞는 담당 배우를 선택한다. 애플리케이션이 어떻게 동작해야 할지 전체 구성(Configuration)을 책임진다.
* 이제 각 배우들은 담당 기능을 실행하는 책임만 지면 된다.
* ```OrderServiceImpl```은 기능을 실행하는 책임만 지면 된다.

OrderServiceImpl의 경우 인터페이스만 보고 개발을 하면 된다. 구체 클래스에 대해 고민할 필요가 전혀 없다. 실행만 책임지면 되는 것이다.

기존에 너무 많은 역할과 책임이 MemberServiceImpl과 OrderServiceImpl에 있었다. 이것들을 역할과 책임을 적절하게 잘 분리했다. (어찌 보면 단일 책임 원칙 SRP를 잘 지킨 것이라 볼 수 있겠다.)


## 4. AppConfig 리팩터링

이번에는 AppConfig를 리팩터링 해볼 것이다.

현재 AppConfig를 보면 중복도 좀 있고, 역할에 따른 구현이 잘 안 보인다.

기대하는 그림은 다음과 같다.

<img width="768" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/abe7c816-6738-49f9-b5ab-1d5baff1e555">

위 그림에서, 클라이언트가 주문할 때 생각해 보면, 주문 서비스 역할이 있었고, 주문 서비스 역할에는 회원 저장소 역할이 있고 할인 정책 역할이 있다. 역할과 구현을 분리해서 한 그림에 보고 싶은데, 구성 정보에는 이게 한 눈에 딱 보여야 한다. 역할이 있고 그 역할에 대한 구현을 어떻게 한다는 것이 한 눈에 보여야 한다.

그런데 지금 AppConfig에는 그런 것이 전혀 보이지 않는다.

이 역할들이 드러나게 하는 것이 굉장히 중요하다.

<br>

현재 AppConfig클래스 코드는 다음과 같다.

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

여기서, memberService에서 ```new MemoryMemberRepository()```를 음영 설정하고, ```cmd + option + M``` 단축키를 누른 후 Name에 'memberRepository'로 설정한다. 리턴 타입은 구체 클래스(MemoryMemberRepository)를 선택하면 안 되고, 인터페이스(MemberRepository)를 선택한다. 

<img width="789" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/8d679273-6ace-45c2-b67c-5c97215a9826">

그러면 위와 같이 창이 뜬다. Process Duplicates 즉 중복이 있었던 것이다.

지금 memberService가 있고, MemberRepository 인터페이스를 반환해주는 역할, OrderService의 역할까지 드러난다. 그런데 한 가지 빠진 것이 있는데, DiscountPolicy이다. 이것은 리팩터링 안 하고 그냥 입력해본다.

수정된 코드는 다음과 같다.

```java
package hello.core;

import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPolicy;
import hello.core.member.MemberRepository;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;

public class AppConfig {

    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    public OrderService orderService() {
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    public DiscountPolicy discountPolicy() {
        return new FixDiscountPolicy();
    }
}
```

위 코드만 봐도, 메서드명을 통해 역할이 잘 드러난다. memberService에 대한 구현을 내 애플리케이션에서는 MemberServiceImpl을 쓸 것이고, memberRepository에 대한 것은 MemoryMemberRepository를 쓸 것이라는 것이 잘 드러난다. 나중에 만약 jdbcMemoryMemberRepository로 바뀐다고 했을 때, 이 코드만 바꾸면 되는 것이다. 그리고, orderService도 보면 orderService에 대한 구체적인 것은 현재는 memberRepository 쓰는 것 가져오고, 애플리케이션에서 결정한 discountPolicy 쓰는 것 가져올 거야 하면 그 아래에 discountPolicy는 Fix이니 Fix를 사용하는 것이다. memberRepository는 MemoryMemberRepository이니까 MemoryMemberRepository 객체가 들어오는 것이다.

그렇게 해서, 이 설계에 대한 그림이 구성 정보에 그대로 드러난다. 역할들이 나오고, 그 역할들에 대한 구현이 어떻게 되는지가 한 눈에 들어오는 것이다.

정리하자면, ```new MemoryMemberRepository()``` 부분이 중복 제거되었다. 이제 ```MemoryMemberRepository```를 다른 구현체로 변경할 때 한 부분만 변경하면 된다.

또, ```AppConfig```를 보면 역할과 구현 클래스가 한 눈에 들어온다. 메서드명과 리턴 타입을 보면 역할이 무엇인지 보인다. 그리고 이것에 대한 구현 클래스가 보인다. 애플리케이션 전체 구성이 어떻게 되어있는지 빠르게 파악할 수 있다는 장점이 있다.

이렇게 설계하는 것이 좋다. 역할을 세우고, 구현이 그 안에 들어가도록 하는 것이다. 그리고 중복도 제거한 것이다.


## 5. 새로운 구조와 할인 정책 적용

이제 정액 할인 정책을 정률 할인 정책으로 변경해볼 것이다.

<b>FixDiscountPolicy → RateDiscountPolicy</b>

기존에 이것을 바꿨을 때에는 클라이언트 코드에 영향을 미쳤었다. 하지만, 현재 구조가 AppConfig가 등장했으므로, AppConfig만 변경하면 된다.

<br>

AppConfig의 등장으로 애플리케이션이 크게 <b>사용 영역</b>과 객체를 생성하고 구성(Configuration)하는 <b>구성 영역</b>으로 분리되었다.

<img width="747" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e8abbe7a-7712-4bfe-b43c-686cb93d0b7e">

완전히 나눠진 것이다. 실제 배우들이 배우의 역할을 하는 영역과, 공연을 기획하고 담당자를 섭외하는 역할이 나눠진 것이다.

만약 할인 정책을 변경하게 되면, 다음과 같이 구성 영역인 AppConfig의 코드만 고치면 된다.

<img width="742" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/76e88ceb-824d-4637-ad04-bc1a71ad9d70">

사용 영역의 코드는 전혀 손댈 필요가 없다.

<br>

AppConfig클래스의 코드를 다음과 같이 수정하면 된다.

<img width="765" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/33ff3326-102f-4576-8fcb-7e0abce6629a">

<br>

이제 테스트를 전부 돌리면, 다음과 같이 잘 돌아간다.

<img width="1143" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/46094606-b2b9-45bb-a648-b98ab18c4aae">

<br>

그리고, OrderApp을 실행시켜보면, 다음과 같이 잘 돌아간다.

<img width="827" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5ffd4f50-c85d-4def-969e-9524215f0896">

여기서 itemPrice를 20000으로 변경하면 다음과 같이 나타난다.

<img width="802" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1d873e1c-5c4c-42fb-944c-1f9afd82e0a3">

RateDiscountPolicy가 적용되었기 때문에, 20000원일 경우 10%인 2000원이 할인되는 것이다. 만약 FixDiscountPolicy가 적용되었을 경우, itemPrice가 20000원이더라도 할인되는 가격은 2000원이 아니라 1000원이다.

<img width="808" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4602e78e-2c66-4d82-9dbb-d13952b04f71">

<br>

정리해보면, 우선 AppConfig에서 할인 정책 역할을 담당하는 구현을 'FixDiscountPolicy' 객체로 변경했다.

이제 할인 정책을 변경해도 애플리케이션의 구성 역할을 담당하는 AppConfig만 변경하면 된다. 클라이언트 코드인 OrderServiceImpl를 포함해서 <b>'사용 영역'</b>인 어떤 코드도 변경할 필요가 없다.

<b>'구성 영역'</b>은 당연히 변경된다. 구성 역할을 담당하는 AppConfig를 애플리케이션이라는 공연의 기획자로 생각하자. 공연 기획자는 공연 참여자인 구현 객체들을 모두 알아야 한다.

OrderServiceImpl은 아무것도 변경할 필요가 없다는 것이 중요한 부분이다.

<br>

<img width="825" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/26417078-66f6-4411-95de-68d44ef075a2">

OrderServiceImpl을 보면, 우선 추상화에 의존하고 있으니 DIP를 지키고 있다. (구체화는 전혀 모르고 있다.) 그리고 OCP도 지키고 있다. FixDiscountPolicy를 RateDiscountPolicy로 변경해도, 클라이언트 코드는 전혀 손대지 않고 있는 것이다.

이렇게 해서 OCP과 DIP를 다 만족하는 코드를 만든 것이다.


## 6. 전체 흐름 정리

지금까지의 흐름을 정리해보자.

* 새로운 할인 정책 개발
* 새로운 할인 정책 적용과 문제점
* 관심사의 분리
* AppConfig 리팩터링
* 새로운 구조와 할인 정책 적용

### "새로운 할인 정책 개발"

다형성 덕분에 새로운 정률 할인 정책 코드를 추가로 개발하는 것 자체는 아무 문제가 없음.

### "새로운 할인 정책 적용과 문제점"

새로 개발한 정률 할인 정책을 적용하려고 하니 <b>클라이언트 코드</b>인 주문 서비스 구현체도 함께 변경해야 했다. <b>OCP를 위반</b>하는 것이다.

왜냐하면 주문 서비스 클라이언트가 인터페이스인 DiscountPolicy뿐만 아니라, 구체 클래스인 FixDiscountPolicy도 함께 의존하고 있었기 때문이다. 이는 <b>DIP를 위반</b>하고 있는 것이다.

그래서 생각을 해보면, 클라이언트 코드인 주문 서비스 구현체가 너무 많은 역할을 하고 있었다. 주문과 관련된 기능도 개발해야 하고, FixDiscountPolicy 객체도 생성해야 하는 등.. 그래서 관심사를 분리했다.

### "관심사의 분리"

애플리케이션을 하나의 공연으로 생각했다. 기존에는 클라이언트가 의존하는 서버 구현 객체를 직접 생성하고, 실행했다. 비유를 하면 기존에는 남자 주인공 배우가 공연도 하고, 동시에 여자 주인공도 직접 초빙하는 다양한 책임을 가지고 있는 것이다.

공연을 구성하고, 담당 배우를 섭외하고, 지정하는 책임을 담당하는 별도의 "공연 기획자"가 나와야 하는 시점이다.

그래서 공연 기획자인 AppConfig가 등장했다. AppConfig는 애플리케이션의 전체 동작 방식을 구성(config)하기 위해, <b>구현 객체를 생성</b>하고 <b>연결</b>하는 책임을 가지게 된다.

이제부터 클라이언트 객체는 자신의 역할을 실행하는 것에만 집중하면 되고, 권한이 줄어든 것이다. (책임이 명확해진 것)

### "AppConfig 리팩터링"

그리고 AppConfig를 리팩터링 했다. 객체지향 설계에서 중요한 것은 역할과 구현을 명확하게 분리하는 것이다.

구성 정보에서 역할과 구현을 명확하게 분리했다. 역할이 잘 드러나게 리팩터링 했고, 중복을 제거(new MemoryMemberRepository를 두 번 한 것을 한 번만 하도록 수정)했다.

### "새로운 구조와 할인 정책 적용"

이렇게 구조를 이쁘게 다 바꾸고 나서, 새로운 구조의 할인 정책을 적용했다.

무조건 1000원이 할인되는 정액 할인 정책에서 10% 할인되는 정책으로 변경했다. 그런데 AppConfig의 등장으로 애플리케이션이 크게 <b>"사용 영역"</b>과 객체를 생성하고 <b>"구성하는 영역"</b>으로 분리되어 있었다. 이 덕분에 할인 정책을 변경하더라도, AppConfig가 있는 구성 영역만 변경하면 되었다.

사용 영역은 전혀 변경할 필요가 없는 것이다. 물론 제일 중요한 DiscountPolicy의 클라이언트인 주문 서비스 코드도 전혀 변경할 필요가 없었다.

이렇게 해서 DIP, OCP를 다 지킬 수 있게 된 것이다.


## 7. 좋은 객체 지향 설계의 5가지 원칙의 적용

이번에는 좋은 객체 지향 설계의 5가지 원칙이 어떻게 적용되어 있는지 정리해볼 것이다.

여기서는 크게 3가지, SRP, DIP, OCP가 적용되어 있다.

### SRP 단일 책임 원칙

<b>"한 클래스는 하나의 책임만 가져야 한다."</b>

클라이언트 객체(OrderService 등)는 기존에 직접 구현 객체를 생성하고, 연결하고, 실행하는 다양한 책임을 가지고 있었다.

SRP 단일 책임 원칙을 따르면서 관심사를 분리했다. 그랬더니 구현 객체를 생성하고 연결하는 책임은 AppConfig가 담당하도록 넘겨버렸다. 그리고 클라이언트 객체는 실행하는 책임만 담당하도록 설계를 변경했다.

### DIP 의존관계 역전 원칙

<b>프로그래머는 "추상화에 의존해야지, 구체화에 의존하면 안 된다." 의존성 주입은 이 원칙을 따르는 방법 중 하나다.</b>

새로운 할인 정책을 개발하고 적용하려고 하니, 클라이언트 코드도 함께 변경해야 했다. 왜냐하면 기존 클라이언트 코드 OrderServiceImpl은 DIP를 지키며 DiscountPolicy 추상화 인터페이스에 의존하는 것 같았지만, FixDiscountPolicy 구체화 구현 클래스에도 함께 의존했기 때문이다.

클라이언트 코드가 DiscountPolicy 추상화 인터페이스에만 의존하도록 코드를 변경했다. 하지만 NullPointerException이 발생했다. 클라이언트 코드는 인터페이스만으로는 아무것도 실행할 수 없다.

AppConfig가 FixDiscountPolicy 객체 인스턴스를 클라이언트 코드 대신 생성해서 클라이언트 코드에 의존관계를 주입했다. 이렇게 해서 DIP 원칙을 따르면서 문제도 해결했다.

### OCP

<b>"소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다."</b>

다형성 사용하고 클라이언트가 DIP를 잘 지키면 OCP가 적용이 될 수 있는 가능성이 열린다.

우리는 애플리케이션을 사용 영역과 구성 영역으로 나눴다.

AppConfig가 의존관계를 FixDiscountPolicy에서 RateDiscountPolicy로 변경을 하더라도, AppConfig가 변경해서 클라이언트 코드에 주입을 해주기 때문에 클라이언트 코드는 변경하지 않아도 된다.

결과적으로 <b>소프트웨어 요소를 새롭게 확장해도 사용 영역의 변경은 닫혀 있는 것이다!</b>

<br>

이렇게 해서 좋은 객체 지향 원칙 5가지에 대해 정리해 보았다.


## 8. IoC, DI, 그리고 컨테이너

이번에는 IoC, DI, 그리고 컨테이너라는 용어에 대해 알아볼 것이다. 스프링을 공부하다 보면 '제어의 역전(IoC)'이라는 단어를 듣게 된다. 이것은 스프링에만 국한된 것은 아니다.

### 제어의 역전 IoC(Inversion of Control)

제어의 역전이란, 내가 호출하는 것이 아니라 프레임워크가 코드를 대신 호출해주는 것이다. 말 그대로 제어권이 뒤바뀐다고 해서 '제어의 역전'이라고 한다.

기존 프로그램은 클라이언트 구현 객체가 스스로 필요한 서버 구현 객체를 생성하고, 연결하고, 실행한다. 한마디로 구현 객체가 프로그램의 제어 흐름을 스스로 조종했다. 개발자 입장에서는 자연스러운 흐름이다.

반면에 AppConfig가 등장한 이후에 구현 객체는 자신의 로직을 실행하는 역할만 담당한다. 프로그램의 제어 흐름은 이제 AppConfig가 가져간다. 예를 들어서 OrderServiceImpl은 필요한 인터페이스들을 호출하지만 어떤 구현 객체들이 실행될지 모른다.

<img width="821" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b0a03156-c1dc-4807-b0ba-bfc83c84e5f6">

가령, 위 코드를 보면 memberRepository에 대해서 흐름 자체가 제어권이 없는 것이다. 어떤 것이 호출될지 OrderServiceImpl 입장에서는 모르는 것이다.

프로그램에 대한 제어 흐름에 대한 권한은 모두 AppConfig가 가지고 있다. 심지어 OrderServiceImpl도 AppConfig가 생성한다. 

<img width="645" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/58421b56-de25-4a1a-8f13-ef4f5cc5301b">

위 그림을 보면 AppConfig가 MemberServiceImpl, OrderServiceImpl을 쓸 것이라고 결정하는 것을 알 수 있다.

그리고 AppConfig는 OrderServiceImpl이 아닌 OrderService 인터페이스의 다른 구현 객체를 생성하고 실행할 수도 있다. 그런 사실도 모른 채 OrderServiceImpl은 묵묵히 자신의 로직을 실행할 뿐이다.

이렇듯 <u>프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전(IoC)이라 한다.</u>


### 프레임워크 vs 라이브러리

프레임워크가 내가 작성한 코드를 제어하고, 대신 실행하면 그것은 프레임워크가 맞다. (JUnit)

<img width="624" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/a43b16af-f434-4b0d-8bbc-4d3e1282835e">

가령, 위와 같이 MemberServiceTest를 보면, join메서드를 테스트 했다. 나는 이 로직만 개발한 것이다. 이것을 실행하고, 제어권을 가져가는 것은 JUnit이라는 테스트 프레임워크이다. 그냥 실행하는 것도 아니고 자신만의 라이프 사이클이 있다. ```@BeforeEach```를 먼저 실행하고, 그 다음에 ```@Test```를 실행하는 것의 라이프 사이클 속에서 내 것만 callback 식으로 불러지는 것이다. 이렇게 내가 제어권을 가지고 있는 것이 아니고, 나는 그 프레임워크 안에서 필요한 부분만 개발하면 프레임워크가 알아서 적절한 타이밍에 호출이 되는 것이다. 이렇게 호출하는 제어권을 넘기는 것을 '제어의 역전'이라고 한다.

반면에 내가 작성한 코드가 직접 제어의 흐름을 담당한다면 그것은 프레임워크가 아니라 라이브러리이다.

예를 들어서 자바 객체를 XML로 바꾸거나 JSON으로 바꾸는 것이 있다면, 내가 그 라이브러리를 불러다가 직접 호출한다. 이런 것은 라이브러리로 보는 것이다.


### 의존관계 주입 DI(Dependency Injection)

OrderServiceImpl은 DiscountPolicy 인터페이스에 의존한다.

<img width="749" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2d4cf5e9-0455-4b6d-b330-17f13ae5a801">

위의 클래스 다이어그램을 보면, OrderServiceImpl은 DiscountPolicy 인터페이스에 의존하도록 코드를 바꿨다. 예전에는 인터페이스에 의존하는 것뿐만 아니라, FixDiscountPolicy와 같은 구체화에도 의존했다.

그래서 실제 어떤 구현 객체가 사용될지는 이제는 모른다.

위 클래스 다이어그램을 보면, OrderServiceImpl 입장에서는 DiscountPolicy 인터페이스만 알고 있는 것이다. 실제 FixDiscountPolicy가 들어올지, RateDiscountPolicy가 들어올지 전혀 모른다.

의존관계는 <b>정적인 클래스 의존 관계와, 실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계</b>들을 분리해서 생각해야 한다.


### 정적인 클래스 의존관계

클래스가 사용하는 import 코드만 보고 의존관계를 쉽게 판단할 수 있다. 

<img width="809" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/8775c2af-bfe3-4e4a-9308-15c5aaf83d4b">

가령, OrderServiceImpl을 보면 사용하는 것이 MemberRepository, DiscountPolicy, Member 등등을 사용한다. 이것을 정적인 의존관계라고 한다.

<img width="1346" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5bd022c0-8ff8-4789-b303-45d49ccd229b">

정적인 의존관계는 애플리케이션을 실행하지 않아도 분석할 수 있다. 클래스 다이어그램을 보자.

<img width="749" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2d4cf5e9-0455-4b6d-b330-17f13ae5a801">

우리가 OrderServiceImpl라는 코드를 보면, implements 해서 상위 인터페이스에 OrderService가 있음을 알 수 있다. 그리고 OrderServiceImpl은 MemberRepository인터페이스와 DiscountPolicy를 참조하고 있다.

그리고 FixDiscountPolicy는 DiscountPolicy에 의존하고 있다. DiscountPolicy는 아무 곳에도 의존하고 있지 않다. 오직 Member만 사용하고 있다.

<img width="378" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/90c13b78-f437-48e4-aa6b-1d8aa62ae96d">

상속이든, 구현이든 화살표 방향으로만 의존하고 있다는 것이다. FixDiscountPolicy는 DiscountPolicy를 의존하고 있다. 그런데 DiscountPolicy는 아무 것에도 의존하고 있지 않다. (Member 파라미터로 넘겨야 하니깐 Member 정도만 사용하고 있다.)

이렇게 정적인 클래스 의존관계와, 실행 시점에 결정되는 동적인 의존관계를 명확히 구분할 수 있어야 한다.

OrderServiceImpl은 MemberRepository, DiscountPolicy에 의존한다는 것을 알 수 있다. 그런데 이러한 클래스 의존관계만으로는 실제 어떤 객체가 OrderServiceImpl에 주입될지 알 수 없다.

<img width="836" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c57a2eac-c3e5-4f04-bd7d-1fb7e3c609e2">

위의 OrderServiceImpl을 보면, 음영 처리 된 부분의 memberRepository에 메모리가 들어올지, JDBC가 들어올지, discountPolicy에 정액 할인이 들어올지 정률 할인이 올지 이 코드만 보고는 분석이 불가능하다. 실제로 실행을 시켜봐야 아는 것이다.

<br>

이것을 <b>동적인 객체 인스턴스 의존 관계</b>라고 한다.

애플리케이션 실행 시점에 실제 생성된 객체 인스턴스의 참조가 연결된 의존 관계이다.

<br>

객체 다이어그램은 다음과 같다.

<img width="759" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/fc69e692-0962-4a1c-a9f0-03dda0cd9884">

클라이언트는 주문 서비스 구현체를 호출한다. AppConfig에서 orderService를 생성할 때 memberRepository에는 MemoryMemberRepository가 들어가고, discountPolicy에는 RateDiscountPolicy가 들어간다.

<img width="628" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/849f2f30-3aa2-489c-af93-ef9c3668d3b2">

객체 다이어그램은 정적인 것이 아니다. 애플리케이션을 실행할 때마다 동적으로 바뀐다.

애플리케이션 <b>실행 시점(런타임)</b>에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 서버의 실제 의존관계가 연결되는 것을 <b>의존관계 주입</b>이라 한다.

객체 인스턴스를 생성하고 그 참조값을 전달해서 연결된다.

의존관계 주입을 사용하면 클라이언트 코드를 변경하지 않고, 클라이언트가 호출하는 대상의 타입 인스턴스를 변경할 수 있다. 정액 할인 정책을 정률 할인 정책으로 변경할 수 있다는 것이다.

<u>의존관계 주입을 사용하면 정적인 클래스 의존관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽게 변경할 수 있다.</u> 동적인 그림만 바꾼다는 것은, 메모리 회원 저장소를 선택할지 JDBC 저장소를 할지, 정액 할인을 할지 정률 할인을 할지 애플리케이션 코드를 손대지 않고 바꿀 수 있다는 것이다.

이게 의존관계 주입의 장점이다.


### IoC 컨테이너, DI 컨테이너

IoC를 해주는 컨테이너, Dependency Injection을 해주는 컨테이너라고 보면 된다.

AppConfig처럼 객체를 생성하고 관리하면서 의존관계를 연결해주는 것을 IoC 컨테이너 또는 <b>DI 컨테이너</b>라고 한다.

AppConfig가 의존관계의 역전을 일으킨다고 해서 IoC 컨테이너(DI 컨테이너)라고 한다. (IoC 컨테이너라는 용어는 너무 범용적이라는 의견에 따라 누군가 'DI 컨테이너'라고 이름을 바꿨다.)

AppConfig는 의존관계 주입을 대신 해준다. orderServiceImpl객체를 생성할 때, 생성자인 memberRepository와 discountPolicy에 집어넣어주는 것은 AppConfig이다.

스프링이 DI 컨테이너 역할을 한다. 하지만 스프링만 DI 컨테이너 역할을 하는 것이 아니다. 여러 오픈소스가 굉장히 많다. 이런 AppConfig에 대한 용어가 굉장히 많다. 조립을 한다고 해서 '어셈블러', 그리고 오브젝트를 만든다고 해서 '오브젝트 팩토리' 등으로 불리기도 한다.


## 9. 스프링으로 전환하기

드디어 스프링을 써볼 것이다. 지금까지는 프로젝트 세팅할 때 제외하고는 스프링을 사용하지 않았다. 순수한 자바 코드만으로 DI를 적용한 것이다.

이제 스프링을 사용해보겠다.

우선 AppConfig를 스프링 기반으로 바꾼다. 수정된 코드는 다음과 같다.

```java
package hello.core;

import hello.core.discount.DiscountPolicy;
import hello.core.discount.RateDiscountPolicy;
import hello.core.member.MemberRepository;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    @Bean
    public DiscountPolicy discountPolicy() {
//        return new FixDiscountPolicy();
        return new RateDiscountPolicy();
    }
}
```

```@Configuration``` 어노테이션을 붙여준다. 그리고 ```import org.springframework.context.annotation.Configuration;```를 위에 붙여준다. 그 다음에 스프링 빈 ```@Bean```을 다 붙여준다.

이전까지는 AppConfig는 애플리케이션의 <b>설정 정보(구성 정보)</b>를 담당하는 것이었다. 스프링에서는 이러한 설정 정보에 이러한 ```@Configuration```을 적어주게 되어 있다. 그리고 각 메서드에 ```@Bean```이라고 적어준다. 이렇게 되면 이 메서드들이 다 스프링 컨테이너에 등록이 된다.

이제 실제 스프링을 써보도록 해보겠다.

<br>

MemberApp을 열어서, AppConfig 버전에서 스프링을 사용하는 버전으로 바꿔보겠다.

수정한 MemberApp의 코드는 다음과 같다.

```java
package hello.core;

import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MemberApp {

    public static void main(String[] args) {
//        AppConfig appConfig = new AppConfig();
//        MemberService memberService = appConfig.memberService();

        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        MemberService memberService = applicationContext.getBean("memberService", MemberService.class);

        Member member = new Member(1L, "memberA", Grade.VIP);
        memberService.join(member);

        Member findMember = memberService.findMember(1L);
        System.out.println("new member = " + member.getName());
        System.out.println("find Member = " + findMember.getName());
    }
}
```

우선 AppConfig를 사용하는 버전은 주석 처리를 한다. 스프링은 모든 것이 ApplicationContext라는 것으로 시작한다. 이것이 스프링 컨테이너라고 보면 된다. 이게 모든 객체들(```@Bean``` 붙은 것들)을 관리해주는 것이다. 생성을 할 때에는 ```new AnnotationConfigApplicationContext();```를 사용해야 한다. 그리고 그 안에는 파라미터로 ```AppConfig.class```를 넣어주면 된다. 이렇게 하면 AppConfig에 있는 환경 설정 정보를 가지고 스프링이 모든 객체들(```@Bean``` 붙은 것들)을 스프링 컨테이너에다가 넣어서 관리한다.

기존에는 ```appConfig.memberService();```와 같은 방식으로 직접 찾아왔다. 이제는 스프링 컨테이너를 통해서 찾아와야 한다. ```applicationContext.getBean();```을 입력하고, 그 안에 이름을 적어준다. 이름을 memberService로 하고, MemberSerivce.class를 넣는 것이다. 내가 AppConfig에서 memberService를 꺼낼 생각이다. 그러면 기본적으로 이름이 메서드 이름으로 등록이 된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ff5e14fb-020e-437e-96b6-deea05bcf288)

그래서 이 이름인 "memberService"를 입력하고, 두 번째는 타입이다. 반환 타입으로 MemberService.class를 입력해 넣는 것이다.

<br>

이제 실행시켜 보겠다.

<img width="1687" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/21278370-08c1-4454-b0bc-1f9adbf52cf3">

보면, 기존과 조금 다른 것이 있다. 무언가 로그가 나왔다. 'Create shared instance of singleton bean ~~~' 으로 로그가 쭉 나오는데, 이게 스프링 빈에 등록이 되는 것이다. 'appConfig' 위의 다섯 줄은 스프링이 내부적으로 필요로 해서 등록을 하는 스프링 빈이다. appConfig도 등록이 된다. memberService, memberRepository, orderService, discountPolicy는 ```@Bean```을 붙여놨던 것이다. key는 이름(ex. "memberService"), value는 객체 인스턴스(ex. ```new MemberServiceImpl(memberRepository());```)로 해서 스프링 컨테이너에 등록이 된다. 그래서 이제 꺼낼 때에는 스프링 컨테이너에 이름, 타입을 주고 꺼내면 된다. 그러면 그대로 돌아가는 것이다. 이전 코드와 똑같다.

<br>

이번에는 OrderApp에서 해보겠다.

수정된 OrderApp 코드는 다음과 같다.

```java
package hello.core;

import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import hello.core.order.Order;
import hello.core.order.OrderService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class OrderApp {

    public static void main(String[] args) {

//        AppConfig appConfig = new AppConfig();
//        MemberService memberService = appConfig.memberService();
//        OrderService orderService = appConfig.orderService();

        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);

        MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
        OrderService orderService = applicationContext.getBean("orderService", OrderService.class);

        Long memberId = 1L;
        Member member = new Member(memberId, "memberA", Grade.VIP);
        memberService.join(member);

        Order order = orderService.createOrder(memberId, "itemA", 10000);

        System.out.println("order = " + order);
        System.out.println("order.calculatePrice = " + order.calculatePrice());
    }
}
```

윗부분들을 주석 처리해주고, ```ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);```를 입력한다. 그 다음에는 getBean을 입력해서 필요한 것이 MemberService이니 위와 같이 입력한다. 또, OrderService도 필요하니 위와 같이 입력한다.

이제 밑에 코드는 똑같다. memberService, orderService 그냥 기존에 했던 것이 나오는 것이다. orderService를 꺼내면 AppConfig에서 생성한 ```new OrderServiceImpl()```이 튀어나온다.

이제 실행해보면 다음과 같다.

<img width="1686" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5c20f3b6-a23d-41d8-adaa-8da448d8df60">

결과가 완전히 동일하다. (다만 로그가 조금 더 늘고 이름이 좀 더 긴 것 같다.)

<br>

정리하자면 다음과 같다.

### 스프링 컨테이너

<b>ApplicationContext를 스프링 컨테이너라고 한다.</b> 기존에는 개발자가 AppConfig를 사용해서 직접 객체를 생성하고 DI를 했지만, 이제부터는 스프링 컨테이너를 사용한다.

스프링 컨테이너는 ```@Configuration```이 붙은 AppConfig를 <b>설정(구성) 정보</b>로 사용한다. 여기서 ```@Bean```이라 적힌 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다. 이렇게 스프링 컨테이너에 등록된 객체를 스프링 빈이라 한다.

스프링 빈은 ```@Bean```이 붙은 메서드의 명을 스프링 빈의 이름으로 사용한다. (memberService, orderService)

이전에는 개발자가 필요한 객체를 AppConfig를 사용해서 직접 조회했지만, 이제부터는 스프링 컨테이너를 통해서 필요한 스프링 빈(객체)를 찾아야 한다. 스프링 빈은 applicationContext.getBean() 메서드를 사용해서 찾을 수 있다.

기존에는 개발자가 직접 자바 코드로 모든 것을 했다면 이제부터는 스프링 컨테이너에 객체를 스프링 빈으로 등록하고, 스프링 컨테이너에서 스프링 빈을 찾아서 사용하도록 변경되었다. 이제부터는 스프링에게 환경 정보를 던져주고, 찾을 때에는 스프링 컨테이너를 통해서 가져오는 것이다. 그러면 스프링이 환경 정보를 가지고 필요한 것들을 읽어와서 스프링 컨테이너에서 다 관리를 하고, 스프링 컨테이너한테 getBean에서 받아올 수 있다. (```@Autowired```도 이와 관련되어 있다.)

<br>

코드가 약간 더 복잡해진 것 같은데, 스프링 컨테이너를 사용하면 어떤 장점이 있을까?

결론부터 말하자면, 어마어마한 장점이 있다. 스프링 컨테이너가 관리해줌으로써 해줄 수 있는 기능이 어마어마하다. 이것에 대해 차차 알아볼 것이다.