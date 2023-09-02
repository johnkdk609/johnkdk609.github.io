---
layout: post
title: 회원 관리 예제
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-09-01 22:54:00 +0900
---
회원 관리 예제를 한 번 만들어볼 것이다.

목차는 다음과 같다.

* 비즈니스 요구사항 정리
* 회원 도메인과 리포지토리 만들기
* 회원 리포지토리 테스트 케이스 작성
* 회원 서비스 개발
* 회원 서비스 테스트

먼저 비즈니스 요구사항을 정리하고, 그 다음에 회원 도메인과 회원 도메인 객체를 저장하고 불러올 수 있는 저장소인 리포지토리 객체를 만들 것이다.

그리고 회원 리포지토리가 정상 동작하는지 테스트 케이스를 만들 것이고, 실제 비즈니스 로직이 있는 회원 서비스를 만들고 이것이 정상적으로 동작하는지 테스트를 만들 것이다. 여기서 테스트는 JUnit이라는 테스트 프레임워크로 만들 것이다.


## 비즈니스 요구사항 정리

* 데이터: 회원ID, 이름
* 기능: 회원 등록, 조회
* 아직 데이터 저장소가 선정되지 않음(가상의 시나리오)

비즈니스 요구사항은 가장 단순한 것으로 만들 것이다. 단순한 예제를 가지고 스프링 생태계 전반적으로 어떤 식으로 개발이 이뤄지고 동작하는지 알아보는 것이 목표이기 때문이다.

추기로 아직 데이터 저장소가 선정되지 않았다는 가상의 시나리오를 상정할 것이다. 개발자는 개발을 해야 하는데 아직 DB가 선정이 안 된 것이다. 성능이 중요한 데이터베이스를 선택할지, 일반적인 관계형 데이터베이스를 선택할지, 아니면 NoSQL로 할지 등이 안 정해진 것이다.


## 일반적인 웹 어플리케이션 계층 구조

<img width="872" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/25c3120d-45d5-4c13-9901-735eee00d581">

* <b>컨트롤러</b>: 웹 MVC의 컨트롤러 역할
* <b>서비스</b>: 핵심 비즈니스 로직 구현
* <b>리포지토리</b>: 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
* <b>도메인</b>: 비즈니스 도메인 객체 ex) 회원, 주문, 쿠폰 등등 주로 데이터베이스에 저장하고 관리됨

일반적인 웹 어플리케이션들은 보통 컨트롤러, 서비스, 리포지토리, 도메인 객체 그리고 데이터베이스로 구성된다.

<b>컨트롤러(Controller)</b>는 웹 MVC의 컨트롤러 역할 혹은 API 만들 때의 컨트롤러 역할을 말한다.

<b>서비스</b> 클래스에는 핵심 비즈니스 로직이 들어간다. 예를 들어 회원은 중복 가입이 안 된다거나.. 

<b>도메인</b>은 회원, 주문, 쿠폰 등 데이터베이스에 주로 저장하고 관리되는 비즈니스 도메인 객체이다. <b>서비스</b>는 이 '비즈니스 도메인 객체'를 가지고 핵심 비즈니스 로직이 동작하도록 구현한 계층이라고 생각하면 되겠다.


## 클래스 의존관계 설계

클래스 의존 관계는 다음과 같다.

<img width="869" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/262a6890-ed88-4b9f-a93f-d7045a549799">

* 아직 데이터 저장소가 선정되지 않아서, 우선 인터페이스로 구현 클래스를 변경할 수 있도록 설계
* 데이터 저장소는 RDB, NoSQL 등등 다양한 저장소를 고민중인 상황으로 가정
* 개발을 진행하기 위해서 초기 개발 단계에서는 구현체로 가벼운 메모리 기반의 데이터 저장소 사용

우선 회원 비즈니스 로직에 <b>MemberService</b>가 있고, 회원을 저장하는 <b>MemberRepository(회원 리포지토리)</b>는 인터페이스로 설계할 것이다. 왜냐하면 아직 데이터 저장소가 선정되지 않았기 때문이다. 그래서 구현체를 우선 메모리 구현체<b>(MemoryMemberRepository)</b>로 만들 것이다. 일단 개발은 해야 하니 메모리로 단순하게 저장하는 방식을 사용하는 것이다. 향후에 RDB로 할지, JPA로 할지 등등이 선정되었다면 바꿔 끼울 것이다. 그래서 인터페이스가 필요한 것이다.


## 회원 도메인과 리포지토리 만들기

이제 실제로 코딩을 해볼 것이다.

먼저 회원의 경우, hello.hellospring 아래에 'domain' 패키지를 만들 것이다. 그리고 그 안에 Member라는 클래스를 만들 것이다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2cd60fe7-738f-4840-9cae-bd04280ca1c7)

Member클래스에는 앞서 계획한 요구사항들을 넣어야 한다. 아이디 식별자가 있어야 하고, 이름이 있어야 한다. id는 고객이 정하는 아이디가 아니라 데이터의 구분을 위해 시스템이 저장하는 아이디이다. 이름은 그냥 이름이다.

Getter, Setter도 함께 만들어야 한다.

Member클래스의 코드는 다음과 같다.

```java
package hello.hellospring.domain;

public class Member {

    private Long id;
    private String name;

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
}
```

Getter, Setter가 좋냐 싫냐 등등 여러 가지 이야기가 있지만 이번 예제에서는 단순하게 Getter, Setter를 사용할 것이다.

다음에는 회원 리포지토리 인터페이스를 만들 것이다. hello.hellospring 아래에 먼저 repository라는 패키지를 만들고, 인터페이스로 MemberRepository를 생성한다. 리포지토리란 저장소를 뜻하는데, 회원 객체를 저장하는 저장소를 만들 것이다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c0eb4333-e1b1-42e0-9084-3b8764481b3b)

MemberRepository인터페이스의 코드는 다음과 같다.

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;

import java.util.List;
import java.util.Optional;

public interface MemberRepository {

    Member save(Member member);
    Optional<Member> findById(Long id);
    Optional<Member> findByName(String name);
    List<Member> findAll();

}
```

맨 처음에 기능을 save를 만들 것이다. 회원을 저장하면 저장된 회원이 반환된다.

그리고 Optional을 이용해서 findById 즉 아이디로 회원을 찾는 기능을 만들 것이다. 

findByName도 이름으로 회원을 찾는 기능이다.

findAll은 지금까지 저장된 모든 회원의 리스트를 반환하는 것이다.

Optional은 Java 8에 들어간 기능이다. findById, findByName로 가져올 때 이게 null일 수 있다. 없으면 null이 반환될 것이다. 요즘에는 null을 그대로 반환하는 것 대신에 Optional로 감싸서 반환하는 것을 선호한다.

이제 구현체를 만들 것이다. repository 패키지 아래에 MemoryMemberRepository라는 클래스를 만든다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/319f74ce-e5a0-49ef-a687-510719407957)

MemoryMemberRepository클래스의 코드는 다음과 같다.

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;

import java.util.*;

public class MemoryMemberRepository implements MemberRepository {

    private static Map<Long, Member> store = new HashMap<>();
    private static long sequence = 0L;

    @Override
    public Member save(Member member) {
        member.setId(++sequence);
        store.put(member.getId(), member);
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        return Optional.ofNullable(store.get(id));
    }

    @Override
    public Optional<Member> findByName(String name) {
        return store.values().stream()
                .filter(member -> member.getName().equals(name))
                .findAny();
    }

    @Override
    public List<Member> findAll() {
        return new ArrayList<>(store.values());
    }

    public void clearStore() {
        store.clear();
    }
}
```

MemoryMemberRepository는 방금 만들었던 MemberRepository인터페이스를 implements 한 다음에, ```option + enter```를 누르고 'Implement methods'를 클릭한 다음, 전부 선택에서 OK를 하면 된다.