---
layout: post
title: 스프링 핵심 원리 이해 1 - 예제 만들기
description: 스프링 핵심 원리 - 기본편
categories: Spring-SpringBoot
date: 2023-10-19 21:16:00 +0900
---
이번에는 역할과 구현을 나누어서, 즉 인터페이스와 그것을 구현하는 객체를 나눠서 개발하는 것을 해볼 것이다. 그런데 이것을 순수한 자바로만 개발할 것이다.

## 목차

1. 프로젝트 생성
2. 비즈니스 요구사항과 설계
3. 회원 도메인 설계
4. 회원 도메인 개발
5. 회원 도메인 실행과 테스트
6. 주문과 할인 도메인 설계
7. 주문과 할인 도메인 개발
8. 주문과 할인 도메인 실행과 테스트

실제 요구사항이 나중에 변경이 되었을 때 정말 유연하게 대처가 가능한지, 다형성, OCP, DIP가 잘 지켜지고 있는지를 보고, 문제가 있으면 그것을 다음 장 스프링 핵심 원리 이해 2에서 객체지향의 원리를 적용해가면서 문제를 해결할 것이다.

## 1. 프로젝트 생성

이번 시간에는 정말 순수한 자바로만 할 것이다. (스프링을 안 사용할 것이다.) 그런데 프로젝트 세팅을 할 때에는 일단 스프링 부트를 사용하면 편하기 때문에 그렇게 할 것이다. (또 나중에 스프링 적용할 때에도 이 라이브러리들이 필요하기는 하다.)

사전 준비물
- Java 11
- IntelliJ

스프링 부트 스타터 사이트(https://start.spring.io)로 이동해서 스프링 프로젝트를 생성한다.

<img width="1491" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/802568e7-f0e3-4011-8704-77f10e68fb9f">

위와 같이 설정한다. 오른쪽에 Dependencies는 아무것도 선택하지 않을 것이다. 아무것도 선택하지 않으면 스프링 부트가 스프링 코어 쪽 라이브러리랑 가장 간단한 몇 개만 가지고 구성해준다. 그리고 아래에 GENERATE 버튼을 누른다.

그리고 다운로드 받아진 core.zip 파일의 압축을 푼 다음, IntelliJ IDE에서 core파일 안에 있는 build.gradle파일을 'Open as Project'로 연다.

<br>

이제 build.gradle 파일을 열어보면 다음과 같이 나온다.

<img width="1126" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/8a65a02d-b6a3-4409-9f7b-2d1607de1a82">

현재 의존관계가 ```spring-boot-starter``` 관련한 라이브러리와 테스트 관련한 것만 심플하게 들어가 있다. 스프링 핵심 라이브러리만 들어가 있는 것이다.

<br>

이제 main/java/hello.core 디렉토리 안에 있는 CoreApplication파일을 열고 Run을 해준다. 그러면 다음과 같이 된다.

<img width="1724" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/59834362-e6ea-4531-b7f2-c6bab13ee7bf">

하고 그냥 끝나야 한다. 내가 지금 스프링 웹 프로젝트를 넣은 것이 아니기 때문에 그냥 이렇게 실행하고 끝나버리는 것이 맞다.

<br>

한 가지 추가하자면, IntelliJ 상단에 IntelliJ IDEA → Settings.. 에 들어가서, 검색창에 gradle을 입력한 다음, Build and run using과 Run tests using을 Gradle에서 IntelliJ로 바꾼 다음 Apply를 누른다.

<img width="976" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5dd83962-4c62-4f80-87ea-6f3ff6aa108b">

이것을 돌릴 때 gradle을 통해서 돌리면 좀 느리다. 이렇게 변경하면 IntelliJ 통해서 바로 실행하기 때문에 빠르다.

이 정도 세팅하면 프로젝트 환경 설정은 끝이다.


## 2. 비즈니스 요구사항과 설계

이번에는 비즈니스 요구사항과 설계를 해보겠다.

먼저 회원, 주문과 할인 정책의 세 가지 요구사항이 있다. (나는 개발자이고, 기획자가 와서 요구사항을 말하는 것이다.)

* 회원
    * 회원을 가입하고 조회할 수 있다.
    * 회원은 일반과 VIP 두 가지 등급이 있다.
    * 회원 데이터는 자체 DB를 구축할 수 있고, 외부 시스템과 연동할 수 있다. (미확정)
* 주문과 할인 정책
    * 회원은 상품을 주문할 수 있다.
    * 회원은 등급에 따라 할인 정책을 적용할 수 있다.
    * 할인 정책은 모든 VIP는 1000원을 할인해주는 고정 금액 할인을 적용해달라. (나중에 변경될 수 있다.)
    * 할인 정책은 변경 가능성이 높다. 회사의 기본 할인 정책을 아직 정하지 못했고, 오픈 직전까지 고민을 미루고 싶다. 최악의 경우 할인을 적용하지 않을 수도 있다. (미확정)

요구사항을 보면 회원 데이터, 할인 정책 같은 부분은 지금 결정하기 어려운 부분이다. 그렇다고 이런 정책이 결정될 때까지 개발을 무기한 기다릴 수도 없다. (일정은 정해져 있으니..) 앞에서 배운 객체 지향 설계 방법을 사용해야 한다!

<br>

인터페이스를 만들고 구현체를 언제든지 갈아끼울 수 있도록 설계하면 된다.

(참고: 프로젝트 환경설정을 편리하게 하려고 스프링 부트를 사용한 것이다. 지금은 스프링 없는 순수한 자바로만 개발을 진행한다는 점을 꼭 기억해야 한다. 스프링 관련은 한참 뒤에 등장한다.)


## 3. 회원 도메인 설계

이제 요구사항에 입각해 회원 도메인을 설계할 것이다.

* 회원 도메인 요구사항
    * 회원을 가입하고 조회할 수 있다.
    * 회원은 일반과 VIP 두 가지 등급이 있다.
    * 회원 데이터는 자체 DB를 구축할 수 있고, 외부 시스템과 연동할 수 있다. (미확정)

<br>

<b>회원 도메인 협력 관계</b>를 먼저 설계해본다.

<img width="940" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2f4a2da0-6cc7-4bd8-9931-5c7cd698e3f7">

클라이언트가 회원 서비스를 호출한다. 이때 회원 서비스는 두 가지 기능을 제공하는데, 회원가입과 회원조회이다. 그리고 회원 저장소를 별도로 만든다. 왜냐하면 지금 회원 DB를 자체적으로 구축할 수도 있고 외부 시스템과 연동할 수도 있기 때문이다. 그래서 회원 데이터에 접근하는 계층을 따로 만드는 것이다. 그렇게 해서 '회원 저장소' 인터페이스를 만들 것이다.

여기까지는 역할이다. <u>클라이언트의 역할</u>, <u>회원 서비스의 역할</u>, <u>회원 저장소의 역할</u>이다.

그런데 이 회원 저장소 역할의 구현을 메모리 회원 저장소, DB 회원 저장소, 외부 시스템 연동 회원 저장소로 나눌 것이다. 이 셋 중에 하나를 꼽으면 되는 것이다. (구현체를 선택하면 되는 것이다.)

그런데 지금 자체 DB도 선택이 안 됐고, 외부 시스템 연동하는 것들도 다 미확정이다. 그렇다고 개발을 손 놓고 있을 수는 없다. 그래서 메모리 회원 저장소를 간단하게 만들어서 개발을 진행할 것이다. 이 메모리 회원 저장소는 테스트할 때에도 쓰이고 로컬에서 개발할 때에도 쓰일 것이다. 그런데, 메모리이기 때문에 서버가 재부팅되면 데이터가 다 날라갈 것이다. 그래서 개발용으로만 쓰는 것이다. 이렇게 개발하다가 나중에 데이터베이스나 외부 시스템 회원 저장소가 선택이 되면, 그 부분만 구현해서 교체하면 된다.

<br>

이제 실제 구현 레벨로 내려오면, 다음과 같이 <b>회원 클래스 다이어그램</b>이 그려진다.

<img width="930" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/fb7d079f-7885-4911-896e-67899ff4de7b">

MemberService라는 역할을 인터페이스로 만들고, 그것에 대한 구현체로 MemberServiceImpl를 만든다. MemberRepository인터페이스는 회원 저장소의 역할을 하는 것이다. 이것에 대한 구현 클래스로 MemoryMemberRepository와 DbMemberRepository를 둘 것이다. (외부 시스템 연동 회원 저장소를 만들 수도 있다.)

<br>

<b>회원 객체 다이어그램</b>은 실제 서버에 올라오면 객체 간, 메모리 간에 참조가 어떻게 되는지를 그린 것이다.

<img width="947" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/cd24fe9c-482a-41da-944f-db541067576e">

클라이언트는 회원 서비스(MemberServiceImpl)를 바라보고, 회원 서비스는 메모리 회원 저장소를 바라본다. 서버가 떠서 클라이언트 객체가 실제로 참조하는 주솟값에 있는 인스턴스는 MemberServiceImpl인 것이다.

<br>

항상 그림은 개념적으로 크게 세 가지로 그려진다. <b>도메인 협력 관계</b>는 기획자들도 볼 수 있는 것이다. 이것을 바탕으로 개발자가 구체화해서 <b>클래스 다이어그램</b>을 만들어낸다. 이 클래스 다이어그램에는 인터페이스랑 구현체가 다 모인다. 클래스 다이어그램은 실제 서버를 실행하지 않고 클래스들만 분석해서 볼 수 있는 그림이다. 그런데 이 구현체들, 가령 MemoryMemberRepository를 넣을지 DbMemberRepository를 넣을지 등은 동적으로 결정되는 것이다. 즉 서버가 뜰 때 new ~ 해서 넣는 것을 결정하는 것이다. 그래서 클래스 다이어그램만으로는 판단하기 어렵다. 그래서 <b>객체 다이어그램</b>이라는 것이 따로 있다. 서버가 실제로 떠서 클라이언트가 실제로 사용하는 MemberServiceImpl, 그리고 MemoryMemberRepository 이렇게 인스턴스끼리의 참조를 알 수 있는 것이다.


## 4. 회원 도메인 개발

이제 본격적으로 회원 도메인을 개발해볼 것이다. 

<img width="930" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/fb7d079f-7885-4911-896e-67899ff4de7b">

위 다이어그램을 가지고 만들면 된다.

<br>

먼저, main/java/hello.core 안에 member 패키지를 만든다. 그리고 그 안에 등급인 Grade를 Enum으로 만든다. <b>Grade이넘</b>의 코드는 다음과 같다.

```java
package hello.core.member;

public enum Grade {
    BASIC,
    VIP
}
```

요구사항에서 회원 등급에는 BASIC과 VIP 두 가지가 있다고 했기 때문이다.

<br>

이제 회원 엔티티를 만들 것이다. 마찬가지로 member패키지 안에 <b>Member클래스</b>를 만든다. <b>Member클래스</b>의 코드는 다음과 같다.

```java
package hello.core.member;

public class Member {

    private Long id;
    private String name;
    private Grade grade;

    public Member(Long id, String name, Grade grade) {
        this.id = id;
        this.name = name;
        this.grade = grade;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Grade getGrade() {
        return grade;
    }

    public void setGrade(Grade grade) {
        this.grade = grade;
    }
}
```

우선 id, name, grade의 세 가지 회원의 속성을 기입한다. 그리고 단축키 ```cmd + N```을 입력하고 Constructor 버튼을 눌러서 생성자를 불러온다. 또, 같은 단축키를 누르고 Getter and Setter 버튼을 눌러 게터와 세터를 가져온다. 이제 이 메서드를 통해서 private의 값을 세팅할 수 있다.

<br>

이제 member패키지 안에 MemberRepository인터페이스를 생성한다. <b>MemberRepository인터페이스</b>의 코드는 다음과 같다.

```java
package hello.core.member;

public interface MemberRepository {

    void save(Member member);

    Member findById(Long memberId);
}
```

회원을 저장하는 save기능과 회원의 아이디로 회원을 찾는 findById기능 두 가지만 만들 것이다.

<br>

회원 저장소를 만들었으니 이제 구현체를 만들어야 한다. (인터페이스만으로 동작하지는 않기 때문이다.) 구현체는 member패키지 안에 MemoryMemberRepository클래스를 생성한다. (원래 인터페이스와 구현체는 같은 패키지에 두기보다는 다르게 두는 것이 설계 상 좋은데, 그러면 예제가 너무 복잡해지니 간단하게 같은 패키지에 넣겠다.)

<b>MemoryMemberRepository클래스</b>의 코드는 다음과 같다.

```java
package hello.core.member;

import java.util.HashMap;
import java.util.Map;

public class MemoryMemberRepository implements MemberRepository {

    private static Map<Long, Member> store = new HashMap<>();

    @Override
    public void save(Member member) {
        store.put(member.getId(), member);
    }

    @Override
    public Member findById(Long memberId) {
        return store.get(memberId);
    }
}
```

MemberRepository인터페이스를 구현한 것이니 ```implements MemberRepository```를 기입한다. 그리고 단축키 ```option + Enter```를 누르고 implement methods를 누른다. 그리고 저장소이니 Map 같은 것이 필요하다. 그래서 ```private static Map<Long, Member> store = new HashMap<>();```를 입력해서 저장소 store을 만든다. implement 된 메서드에는 생성한 저장소 store에 각각 getId, get(memberId)를 입력한다. 원래 오류처리 등을 해야 하는데, 그것은 이번 예제의 핵심이 아니니 그냥 넘어가겠다. 아직 데이터베이스는 확정되지 않았기 때문에 MemoryMemberRepository로 만들었다. 일단 개발을 진행할 수 있는 것이다.

현재는 HashMap을 사용했는데, 사실은 동시성 이슈가 있을 수 있기 때문에 ConcurrentHashMap을 사용해야 한다. 실무에서는 동시성 이슈가 있어서 ConcurrentHashMap을 쓰겠지만, 예제이니 그냥 간단하게 HashMap을 쓰겠다.

<br>

이번에는 회원 서비스를 만들 것이다. hello.core/member 패키지 안에 MemberService 인터페이스를 생성한다. <b>MemberService인터페이스</b>의 코드는 다음과 같다.

```java
package hello.core.member;

public interface MemberService {

    void join(Member member);

    Member findMember(Long memberId);

}
```

MemberService에는 회원가입과 회원조회의 두 가지 기능이 있어야 한다. 그래서 join과 findMember 두 가지 기능을 추가하였다.

<br>

이제 인터페이스를 만들었으니 구현체를 만들어야 한다. 같은 member 패키지 안에 MemberServiceImpl 클래스를 생성한다. 관례 같은 것인데, 구현체가 하나만 있을 때에는 인터페이스 명 뒤에 Impl이라고 쓴다. <b>MemberServiceImpl클래스</b>의 코드는 다음과 같다.

```java
package hello.core.member;

public class MemberServiceImpl implements MemberService {

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

가입을 하고 회원을 찾으려면 앞에서 만들었던 MemberRepository 인터페이스가 필요하다. 그래서 ```private final MemberRepository memberRepository = new MemoryMemberRepository();```를 입력했다. 인터페이스만 가지고 있으면 nullpointerexception이 발생할 것이다. 만약 join메서드에서 ```memberRepository.save(member);```를 통해 save를 호출해도 구현체가 없는 null이면 nullpointerexception이 발생하는 것이다. 그래서 구현 객체를 선택해줘야 한다. 앞에서 만들었던 MemoryMemberRepository를 추가하는 것이다.

(참고로, 자동완성 되는 단어에 대해 단축키 ```cmd + shift + Enter```를 누르면 마지막에 세미콜론(;)까지 입력해준다.)

join을 해서 save를 호출하면 다형성에 의해서 MemoryMemberRepository에 있는 인터페이스가 아니라, MemoryMemberRepository의 오버라이드(```@Override```) 된 save가 호출된다.


## 5. 회원 도메인 실행과 테스트

이번에는 우리가 만든 회원 도메인이 정상적으로 동작하는지 테스트를 해볼 것이다.

<img width="1331" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/fc547cbf-7414-4668-9231-96f08ce9b4e9">

위 그림을 만들 것이다. 클라이언트가 런타임에 동작을 하면, 클라이언트는 MemberServiceImpl이라는 회원 서비스를 사용하게 되고, 회원 서비스는 메모리 회원 저장소(MemoryMemberRepository)를 사용하게 된다. 클래스 다이어그램은 정적인 것이고, 객체 다이어그램은 동적인 것이다. 왜냐하면, 객체 다이어그램에서는 ```new ~``` 해서 실제로 들어가는 것을 알 수 있기 때문이다.

<br>

먼저 main/java/hello.core 패키지 안에 MemberApp 클래스를 생성한다. 여기서 잘 되는지 테스트를 간단하게 해볼 것이다. <b>MemberApp클래스</b>의 코드는 다음과 같다.

```java
package hello.core;

import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;

public class MemberApp {

    public static void main(String[] args) {
        MemberService memberService = new MemberServiceImpl();
        Member member = new Member(1L, "memberA", Grade.VIP);
        memberService.join(member);

        Member findMember = memberService.findMember(1L);
        System.out.println("new member = " + member.getName());
        System.out.println("find Member = " + findMember.getName());
    }
}
```

```psvm```을 입력하고 엔터를 치면 ```public static void main(String[] args)```가 완성된다. 그리고 ```MemberService memberService = new MemberServiceImple();```를 입력해 MemberServiceImpl을 선택한다. 그 다음에 ```new Member(1L, "memberA", Grade.VIP);```를 입력하고 ```cmd + option + V``` 단축키를 누르고 이름은 member로 만든다. 회원가입(join)을 하기 위해 만든 것이다. 그리고 ```memberService.join(member);```를 입력해서 회원가입을 한다. 아이디는 1(Long 타입이기 때문에 1L로 1 뒤에 L을 붙여줘야 한다. 그냥 1 하면 compile error이 발생한다.), 이름은 "memberA", 등급은 VIP로 설정한 것이다.

이제 이 사람이 잘 가입이 되었는지 확인을 해보겠다. ```memberService.findMember(1L);```를 입력하고 ```cmd + option + V```를 누른 다음 이름은 findMember로 한다. 가입한 멤버와 find한 멤버가 똑같으면 내가 원하는 대로 될 것이다. 이제 단순하게 찍어볼 것이다. ```soutv```를 입력하고 엔터를 누르면 변수를 선택할 수 있다. 찾은 멤버 member와 findMember를 선택한다. 그리고 각자 ```.getName()```을 붙여 이름을 출력해볼 것이다. 이제 애플리케이션을 실행하면 콘솔창에 다음과 같이 나타난다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/48627da8-55e8-45c7-93c2-0f936edd4b5b)

원하는 결과가 제대로 나왔다.

<br>

이런 방법은 순수한 자바 코드의 자바 메서드를 실행한 것이고, 스프링 관련한 코드는 전혀 없다. 순수한 자바로만 개발을 한 것이다. 그런데 애플리케이션 로직으로 이렇게 메인 메서드를 테스트하는 것은 한계가 있다. 그래서 더 나은 테스트 방법인 JUnit 테스트 프레임워크를 사용할 것이다.