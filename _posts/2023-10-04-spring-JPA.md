---
layout: post
title: 스프링 DB 접근 기술 - JPA
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-10-04 16:05:00 +0900
---
이번에는 JPA에 대해 알아볼 것이다.

방금 전에는 Jdbc를 해보고, JdbcTemplate으로 바꿨다. Jdbc에서 JdbcTemplate으로 바꿔보니 반복적인 코드가 확 줄었다. 그런데 아직도 해결이 안 되는 것이 하나 있는데, SQL은 결국 개발자가 작성을 해야 했다.

그런데 JPA라는 기술을 사용하면 그런 SQL query도 JPA가 자동으로 처리해준다. 그렇게 해서 개발 생산성을 크게 높일 수 있다. 마치 객체를 메모리에 넣듯이 JPA에 집어 넣으면, DB에 SQL 날리고 데이터 DB를 통해서 가져오는 것을 JPA가 중간에서 다 처리해준다.

단순히 SQL을 만들어주는 것을 넘어서서 JPA를 사용하면 SQL보다는 객체 중심으로 고민할 수 있다. SQL과 데이터 중심 설계에서 객체 중심 설계로 패러다임을 전환할 수 있다. 결과적으로 JPA를 사용하면 개발 생산성을 크게 높일 수 있다.

<br>

정리하자면 다음과 같다.

* JPA는 기존의 반복 코드는 물론이고, 기본적인 SQL도 JPA가 직접 만들어서 실행해준다.
* JPA를 사용하면, SQL과 데이터 중심의 설계에서 객체 중심의 설계로 패러다임을 전환할 수 있다.
* JPA를 사용하면 개발 생산성을 크게 높일 수 있다.

<br>

JPA를 사용하려면 build.gradle을 수정해야 한다.

<img width="702" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/099592c2-1aa8-44b7-9786-bfa0b5ca28f1">

위와 같이 jdbc는 주석 처리하고, data-jpa를 추가한다. 그리고 gradle refresh를 한다.

<br>

그 다음에는 application.properties 파일을 열고 JPA와 관련된 설정을 추가해줄 것이다.

<img width="546" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b81275cb-6b40-46c4-81b8-ea092f65c9fd">

```spring.jpa.show-sql=true```를 입력하면 JPA가 날리는 SQL을 볼 수 있다. 그리고 JPA를 사용하면 회원 객체를 보고 테이블을 다 만든다. 그런데 현재 테이블이 만들어져 있고 만들어진 것을 쓸 것이기 때문에, ```spring.jpa.hibernate.ddl-auto=none```를 입력해 자동으로 테이블 생성해주는 기능을 끄고 시작할 것이다.

<br>

그 다음에 JPA을 쓰려면 Entity를 매핑해줘야 한다. 라이브러리가 다 받아지고 나면 External Libraries에 아래와 같이 hibernate 라이브러리가 있어야 한다.

<img width="575" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b6bc9705-70c5-4c27-bb4f-478a5a2a5dcc">

JPA라는 것은 인터페이스이다. 인터페이스만 제공이 되는 것이고, 그 구현체로 hibernate, eclipse 등 여러 개의 벤더들이 있다. 그 중에서 우리는 JPA 인터페이스의 hibernate만 거의 쓴다고 보면 된다. 

JPA라는 것은 Java 진영의 표준 인터페이스이고, 구현은 여러 업체들이 하는 것이라 보면 된다. 각 업체마다 성능이 더 좋거나 더 쓰기 편한 것들이 있을 것이다.

JPA는 객체와 ORM이라는 기술이다. 객체(Object)와 관계형 데이터베이스(Relational Database)의 테이블을 매핑(Mapping) 한다는 것이다. 매핑은 어노테이션으로 한다.

<br>

수정한 Member클래스는 다음과 같다.

```java
package hello.hellospring.domain;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Member {

    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
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

```@Entity``` 어노테이션을 위에 붙인다. 그러면 이것은 JPA가 관리하는 Entity가 되는 것이다. 그리고 ```@Id```를 붙여서 pk를 매핑해준다. pk는 insert를 할 때 DB에서 자동으로 값을 생성해주고 있다. 이것을 <b>IDENTITY 전략</b>이라고 한다. 그래서 ```GeneratedValue(strategy = GenerationType.IDENTITY)```라고 입력한다.

이름을 만약 column명이 username이면 ```@Column(name = "username")```을 name위에 붙이면 된다. (하지만 name의 경우 DB에서도 이름이 name이기 때문에 그대로 가면 된다.) 이렇게 어노테이션을 가지고 Database랑 매핑하는 것이다. 그렇게 해 놓으면 이 정보를 가지고 insert, update, delete, select문을 다 만들 수 있다.

<br>

이제 리포지토리를 만들어볼 것이다.

hello.hellospring/repository 디렉토리 안에 JpaMemberRepository를 만들고, MemberRepository를 implements 한다.

JpaMemberRepository클래스의 코드는 다음과 같다.

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;

import javax.persistence.EntityManager;
import java.util.List;
import java.util.Optional;

public class JpaMemberRepository implements MemberRepository {

    private final EntityManager em;

    public JpaMemberRepository(EntityManager em) {
        this.em = em;
    }

    @Override
    public Member save(Member member) {
        em.persist(member);
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        Member member = em.find(Member.class, id);
        return Optional.ofNullable(member);
    }

    @Override
    public Optional<Member> findByName(String name) {
        List<Member> result = em.createQuery("select m from Member m where m.name = :name", Member.class)
                .setParameter("name", name)
                .getResultList();

        return result.stream().findAny();
    }

    @Override
    public List<Member> findAll() {
        return em.createQuery("select m from Member as m", Member.class)
                .getResultList();
    }
}
```

그리고 메서드들을 모두 import 한다. ```private final EntityManager em;```을 기입하고 생성자를 붙여야 한다. JPA는 EntitiyManager라는 것으로 모든 것이 동작한다. 아까 build.gradle에 data-jpa 라이브러리를 받았는데, 그렇게 하면 스프링 부트가 자동으로 EntityManager를 생성해준다. 현재 데이터베이스랑 다 연결해서 만들어주는 것이다. 그래서 우리는 이 만들어진 것을 injection 받으면 된다.

<b>결론적으로 JPA를 쓰려면 EntityManager를 주입 받아야 한다.</b>

이제 save메서드 쪽에서 ```em.persist(member);```를 기입한다. persist는 '영속하다', '영구 저장하다'의 뜻이다. return값이 없기 때문에 ```return member;```를 기입하면 끝난다. 이렇게 하면 JPA가 insert query 다 만들어서 DB에 집어넣고, id까지 member에다가 setId 다 해준다.

findById메서드에서는 ```Member member = em.find(Member.class, id);```를 기입한다. 조회할 타입과 식별자(pk) 값을 넣어주면 조회가 되는 것이다. 리턴을 해야 하는데, Optional로 반환하기 때문에 ```return Optional.ofNullable(member);```를 기입하면 된다.

findByName메서드의 경우 조금 특별한 jpql이라는 객체지향 query 언어를 써야 한다. (거의 SQL과 비슷하다.) findByName으로 하나만 찾으니 ```return result.stream().findAny()```를 기입해준다.

findAll메서드의 경우에도 jpql이라는 query 언어를 사용한다. 우리가 보통 테이블 대상으로 sql을 날리는데, 그게 아니라 객체를 대상으로 query를 날리는 것이다. 그러면 이게 sql로 번역이 된다. 정확하게 Entity를 대상으로 query를 날리는 것이다. 흥미로운 점은 sql의 경우 ```select * from ~~``` 이렇게 할 텐데, 이 경우 ```select m from ~~```으로 Member Entity 자체를 select 한다.

findByName 혹은 findAll와 같이 List를 가지고 돌릴 때에는 pk 기반이 아니므로 jpql을 작성해줘야 한다.

<br>

이 JPA 기술을 스프링에 한 번 감싸서 제공하는 기술이 있는데, 그것이 '스프링 데이터 JPA'이다. 이것을 사용하면 jpql을 안 짜도 된다.

JPA를 쓸려면 주의해야 하는 점은 항상 서비스 계층에 ```@Transactional``` 어노테이션이 있어야 한다. 데이터를 저장하거나 변경할 때 필요한 것이다. 위에 붙이면 된다.

<img width="311" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/666b9586-9255-4345-b903-bbef4d099fda">

JPA는 join 들어올 때 모든 데이터 변경이 Transactional 안에서 실행되어야 한다.

<br>

이제 SpringConfig클래스를 수정한다.

수정한 SpringConfig클래스의 코드는 다음과 같다.

```java
package hello.hellospring;

import hello.hellospring.repository.*;
import hello.hellospring.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.persistence.EntityManager;
import javax.sql.DataSource;

@Configuration
public class SpringConfig {

    private EntityManager em;

    @Autowired
    public SpringConfig(EntityManager em) {
        this.em = em;
    }

    //    private final DataSource dataSource;
//
//    @Autowired
//    public SpringConfig(DataSource dataSource) {
//        this.dataSource = dataSource;
//    }

    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
//        return new MemoryMemberRepository();
//        return new JdbcMemberRepository(dataSource);
//        return new JdbcTemplateMemberRepository(dataSource);
        return new JpaMemberRepository(em);
    }
}
```

아래에 ```return new JpaMemberRepository(em);```을 입력해준다. 그리고 위에 EntityManager를 받아온다.

<br>

이제 MemberServieIntegrationTest를 돌려보겠다.

<img width="1685" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4e4d02d2-077a-4fba-ac38-02df5c1ca3ff">

한 번에 잘 작동한다.

중간에 Hibernate query들이 보인다. 기본적으로 스프링 데이터 JPA를 세팅할 때 hibernate 구현체가 사용이 된다.

확실시 하기 위해 ```@Test``` 어노테이션 위에 ```@Commit``` 어노테이션을 붙이고 다시 실행시켜 보겠다. 그러면 DB에 반영이 된다. (원래 Test에 Transactional이 있으면 자동으로 rollback 되었었다.) ```member.setName("spring100");```으로 수정하고 다시 실행한 다음, h2 안에 들어가서 보면 아래와 같이 잘 들어가 있는 것을 볼 수 있다.

<img width="726" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/9c8356c5-91f3-4e76-adca-31a444d1569f">

(이제 ```@Commit``` 어노테이션을 제거한다.)