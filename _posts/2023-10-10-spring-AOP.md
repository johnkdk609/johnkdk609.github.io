---
layout: post
title: AOP (Aspect Oriented Programming)
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-10-10 13:04:00 +0900
---
이제 AOP(Aspect Oriented Programming)에 대해 알아볼 것이다.

## AOP가 필요한 상황

* <b>공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern)</b>

모든 메서드의 호출 시간을 측정하고 싶은 상황을 가정해보겠다.

이것을 일일이 다 하는 경우를 생각해보자.

<b>MemberService클래스</b>에서 join메서드, findMembers메서드의 호출 시간을 측정하려면 다음과 같이 수정해야 한다.

```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Optional;

@Transactional
public class MemberService {

    private final MemberRepository memberRepository;

    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    /**
     * 회원가입
     */
    public Long join(Member member) {

        long start = System.currentTimeMillis();

        try {
            validateDuplicateMember(member);    // 중복 회원 검증
            memberRepository.save(member);
            return member.getId();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("join = " + timeMs + "ms");
        }
    }

    private void validateDuplicateMember(Member member) {
        memberRepository.findByName(member.getName())
                        .ifPresent(member1 -> {
                            throw new IllegalStateException("이미 존재하는 회원입니다.");
                        });
    }

    /**
     * 전체 회원 조회
     */
    public List<Member> findMembers() {

        long start = System.currentTimeMillis();
        
        try{
            return memberRepository.findAll();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("findMembers " + timeMs + "ms");
        }
    }

    public Optional<Member> findOne(Long memberId) {

        return memberRepository.findById(memberId);
    }

}
```

위의 경우 두 개 메서드에 시간을 측정하기 위헤 코드를 추가했다. 그런데 만약 메서드가 1000개이고 그 중 2개만 한 것이라고 가정하면 막막하다. 이런 노가다 작업 하다가 야근 하고 하루가 가는 것이다.

이제 HelloSpringApplication을 실행시키고, 웹 브라우저에 localhost:8080으로 접속한다. 여기서 '회원 목록' 버튼을 누르면 IntelliJ 콘솔에 다음과 같이 나타난다.

<img width="176" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/593fb186-fc2f-465c-a915-4a19fd028bff">

'회원 가입' 버튼을 누르고 "spring1"을 등록하니 콘솔에 다음과 같이 뜬다.

<img width="118" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b177c8af-6584-471e-8e2c-508de9cd78fb">

<br>

위 방식의 문제점은 다음과 같다.

* 회원가입, 회원 조회에 시간을 측정하는 기능은 핵심 관심 사항이 아니다.
* 시간을 측정하는 로직은 공통 관심 사항이다.
* 시간을 측정하는 로직과 핵심 비즈니스 로직이 섞여서 유지보수가 어렵다.
* 시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵다.
* 시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 한다.