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

<br>

이제 테스트를 돌려볼 것이다.

MemberServiceIntegrationTest 클래스를 열고, 회원가입 메서드부터 돌려본다.

<img width="1685" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/22ee999c-1bed-4a30-8d56-af48ffc18e8b">

정상적으로 작동한다.

통합 테스트도 마찬가지로 잘 작동한다.

<br>

## 원리 정리

스프링 데이터 JPA가 SpringDataJpaMemberRepository 인터페이스를 보고 proxy라는 기술을 이용해 객체를 생성해서 스프링 빈으로 자동 등록해준다. 그러면 우리는 그것을 injection 해서 썼던 것이다.

그러면 기본적인 save 등은 어디 갔는가?

여기에 있다.

<img width="699" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/15351c59-b18f-4f92-991f-4461ffb328bd">

직전에 생성한 SpringDataJpaMemberRepository 인터페이스에서 JpaRepository를 extends 한다. 이 JpaRepository에 마우스 커서를 갖다 대고 ```Alt```를 누른 채 클릭하면 JpaRepository에 들어갈 수 있다.

<br>

JpaRepository 인터페이스에 들어가 보면 기본 메서드들이 다 제공된다. findAll(), findById(), save() 등등 다 있다. (사실 김영한 선생님이 처음 예제를 이것과 맞춰놓은 것이라고 한다.) 기본적인 CRUD와 단순 조회들이 다 제공되는 것이다.

그래서 여기서는 우리가 생각할 수 있는 공통의 것들을 가져다 쓰면 된다.

그런데 못 만드는 것이 있다.

위 그림을 보면, 스프링 데이터 JPA가 기본적으로 JpaRepository에서 findAll(), save() 등이 다 제공되고, 스프링 데이터를 보면 (약간 옛날 버전이라서 차이가 조금 있기는 하지만) CrudRepository에서 기본적인 save 등이 다 제공된다. 머릿속으로 상상할 수 있는 공통화할 수 있는 것들을 제공해주기에 가져다 쓰면 되는 것이다.

<br>

그런데 아무리 개발자가 공통화를 해도 이것은 모두에게 통용되는 것이다. 그런데 예를 들어서 Member클래스에서 'name으로 찾고 싶다', 혹은 'email 이름으로 찾고 싶어'와 같은 것은 비즈니스가 다 다르기 때문에 공통화하는 것이 불가능하다. 그래서 공통 클래스로 제공할 수 없다.

이름으로 찾는데 내 프로젝트야 "name"이지만 다른 회사에서는 "username" 이라고 할 수도 있고, 또 다른 객체가 매우 많다. 주문할 때에도 주문서 번호로 조회할 수도 있고, 고객이 주문한 상품 이름으로 조회하는 등 인터페이스로 공통화할 수 없는 것이다.

그래서 스프링 데이터 JPA에게 ```findByName```을 직접 입력한다. 그러면 뒤에 "name"이 있으니, JPA가 어떤 식으로 query를 짜냐면 ```// select m from Member m where m.name = ?```으로 JPQL을 짜준다. 그러면 이게 SQL로 번역이 되어서 실행된다. 여기에 규칙이 있다. 인터페이스 이름만으로도 개발이 끝난 것이다. 

<br>

<b>스프링 데이터 JPA 제공 기능</b>은 다음과 같다.

* 인터페이스를 통한 기본적인 CRUD
* ```findByName()```, ```findByEmail()```처럼 이름만으로 조회 기능 제공
* 페이징 기능 자동 제공

(참고: 실무에서는 JPA와 스프링 데이터 JPA를 기본으로 사용하고, 복잡한 동적 쿼리는 Querydsl이라는 라이브러리를 사용하면 된다. Querydsl을 사용하면 쿼리도 자바 코드로 안전하게 작성할 수 있고, 동적 쿼리도 편리하게 작성할 수 있다. 이 조합으로 해결하기 어려운 쿼리는 JPA가 제공하는 네이티브 쿼리를 사용하거나, 앞서 학습한 스프링 JdbcTemplatefmf 사용하면 된다.)