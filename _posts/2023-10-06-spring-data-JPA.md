---
layout: post
title: 스프링 DB 접근 기술 - 스프링 데이터 JPA
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-10-06 15:03:00 +0900
---
이번에는 스프링 데이터 JPA에 대해 알아볼 것이다.

스프링 부트, JPA만 사용해도 개발 생산성이 매우 크게 증가하고, 개발해야 할 코드도 확 줄어든다. 여기에다가 스프링 데이터 JPA를 사용하면 리포지토리에 구현 클래스 없이 인터페이스만으로 개발을 완료할 수 있다. (물론 디테일하게 들어가면 좀 다를 수 있다.) 그리고 반복적으로 개발해온 CRUD 기본적인 기능도 스프링 데이터 JPA가 다 제공해준다. 내가 코드를 적을 게 따로 없는 것이다.

스프링 부트와 JPA라는 기반 위에, 스프링 데이터 JPA라는 환상적인 프레임워크를 더하면 개발이 정말 즐거워진다. 지금까지 조금이라도 단순하고 반복이라 생각했던 개발 코드들이 확연하게 줄어들게 된다. 따라서 개발자는 핵심 비즈니스 로직을 개발하는 데 집중할 수 있다.

실무에서 관계형 데이터베이스를 사용한다면 스프링 데이터 JPA는 이제 선택이 아니라 필수이다.

> <b>주의</b>: 스프링 데이터 JPA는 JPA를 편리하게 사용하도록 도와주는 기술이다. 따라서 <u>JPA를 먼저 학습한 후에 스프링 데이터 JPA를 학습해야</u> 한다.

(우아한형제들의 경우 기본적인 기술 스택으로 스프링 데이터 JPA, JPA, 스프링 부트 이렇게 기본으로 깔고 간다. 주변의 여러 회사들도 JPA를 많이 도입하고 있는 상황이다.)

<br>

앞의 JPA 설정을 그대로 사용한다.

main/java/hello.hellospring/repository 안에 SpringDataJpaMemberRepository 인터페이스를 생성한다.

<b>SpringDataJpaMemberRepository 인터페이스</b>의 코드는 다음과 같다.

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface SpringDataJpaMemberRepository extends JpaRepository<Member, Long>, MemberRepository {

    @Override
    Optional<Member> findByName(String name);
}
```

인터페이스가 인터페이스를 받을 때에는 extends를 사용한다. 첫번째 T는 Member, 두 번째 id는 Entity에서 식별자 pk로 타입은 Long이다. 인터페이스는 다중상속이 되기 때문에 MemberRepository로 extends 한다.

그리고 findByName 메서드를 오버라이드 한다. 이렇게 하면 다 만든 것이다. 더 구현할 것이 없다.

이렇게 스프링 데이터 JPA가 JpaRepository를 받고 있으면 구현체를 자동으로 만들어준다. 그래서 스프링 빈에 자동으로 등록한다. 내가 스프링 빈에 등록하는 것이 아니라, 스프링 데이터 JPA가 이것을 보고 구현체를 만들어서 등록해주는 것이다. 우리는 그것을 가져다 쓰면 된다.

<br>

이제 SpringConfig클래스를 열고, 다음과 같이 수정한다.

```java
package hello.hellospring;

import hello.hellospring.repository.*;
import hello.hellospring.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SpringConfig {

    private final MemberRepository memberRepository;

    @Autowired
    public SpringConfig(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
    
    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository);
    }

}
```

위에 ```private final MemberRepository memberRepository;```를 기입하고 Constructor를 받는다. 이렇게 하면 스프링 데이터 JPA가 만들어놓은 구현체가 등록이 된다. (생성자가 하나인 경우에는 생략해도 되지만, 그냥 ```@Autowired``` 어노테이션을 붙였다.)

이제 memberService에 의존관계를 세팅해줘야 한다. 스프링 컨테이너에서 memberRepository를 찾는다. 그런데 지금 등록한 것이 없다. 그런데 하나가 있다. <b>SpringDataJpaMemberRepository</b>이다. 인터페이스만 만들어 놓고 JpaRepository를 extends 해두면 스프링 데이터 JPA가 인터페이스에 대한 구현체를 어떤 기술을 가지고 만들어 놓는다. 그리고 스프링 빈에 등록해둔다. 그래서 우리는 injection을 받을 수 있다.