---
layout: post
title: 스프링 DB 접근 기술 - 스프링 종합 테스트
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-09-27 15:38:00 +0900
---
이번에는 스프링까지 다 올리고 DB까지 다 연결해서 동작하는 통합 테스트를 해볼 것이다.

이전해 했던 테스트 MemoryMemberRepositoryTest의 경우, 전혀 스프링과 관련이 없고 순수하게 자바 코드를 가지고 했던 테스트였다. 그런데 순수한 자바 코드를 가지고 지금 테스트를 할 수 없다. 왜냐하면 데이터베이스 커넥션 정보도 스프링 부트가 들고 있기 때문이다. 이제부터는 테스트를 스프링과 엮어서 해볼 것이다.

우선 test/java/hello.hellospring/service 디렉토리 안에 있는 <b>MemberServiceTest</b>를 한 번 돌려보겠다.

<img width="1243" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/36971c6b-82ab-4b7c-931b-82e128f87c30">

매우 빨리 실행된다. 이것은 자바 JVM 안에서 끝나는 것이다.

<br>

이제 DB까지 연결한 테스트를 만들 것이다.

MemberServiceTest를 클릭하고, ctrl + C, ctrl + V 한 다음 MemberServiceIntegrationTest로 명명한 다음 생성한다. 그리고 수정할 것이다.

<b>MemberServiceIntegrationTest클래스</b>의 코드는 다음과 같다.

```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.transaction.annotation.Transactional;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

@SpringBootTest
@Transactional
class MemberServiceIntegrationTest {

    @Autowired MemberService memberService;
    @Autowired MemberRepository memberRepository;

    @Test
    void 회원가입() {
        // given
        Member member = new Member();
        member.setName("spring");

        // when
        Long saveId = memberService.join(member);

        // then
        Member findMember = memberService.findOne(saveId).get();
        Assertions.assertThat(member.getName()).isEqualTo(findMember.getName());
    }

    @Test
    public void 중복_회원_예외() {
        // given
        Member member1 = new Member();
        member1.setName("spring");

        Member member2 = new Member();
        member2.setName("spring");

        // when
        memberService.join(member1);
        IllegalStateException e = assertThrows(IllegalStateException.class, () -> memberService.join(member2));

        assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");

        // then
        
    }
}
```

우선 ```@SpringBootTest``` 어노테이션과 ```@Transactional``` 어노테이션을 위에 붙인다. 스프링을 테스트할 때 매우 편리하게 ```@SpringBootTest```를 붙이면 된다.

그리고 ```@BeforeEach```, ```@AfterEach``` 어노테이션이 붙어 있는 코드들은 지웠다. 직접 객체를 생성해서 넣을 때 사용한 코드들인데, 이제는 스프링 컨테이너에게 '멤버 서비스, 멤버 리포지토리를 내놔' 해야 한다. 그때는 Contructor를 사용했다. 그런데 테스트는 제일 끝단에 있는 것이기 때문에, 테스트 코드를 만들 때에는 제일 편한 방법을 쓰면 된다. 그냥 ```@Autowired```를 붙이면 되는 것이다. 테스트를 다른 데에 갖다 쓸 것이 아니기 때문에, 그냥 필드 기반으로 ```@Autowired``` 받는 것이 매우 편하다.

그리고 MemoryMemberRepository를 ```MemberRepository```로 바꾼다. 이렇게 하면 현재 스프링에 등록되어 있는, 구현체는 스프링이 configuration 한 곳에서 올라올 것이다.

```@AfterEach``` 어노테이션이 붙어있는 코드의 경우 메모리 DB에 있는 데이터를 다음 테스트에 영향을 주지 않기 위해 지워주려고 했던 것이다. 위에 있는 ```@Transactional``` 어노테이션 덕분에 이제 이 부분은 필요가 없으므로 지운다.

스프링을 동작하고 '회원가입()' 부분만 테스트를 돌려보면 에러가 발생한다. 이유는 DB에 데이터가 이미 있기 때문이다.

그러므로 DB의 데이터를 지워줘야 한다. h2.sh를 실행시킨 후, ```delete from member```를 입력하고 ```ctrl + Enter```를 눌러 데이터를 지운다. 이제 다시 '회원가입()' 메서드를 실행하면 잘 동작한다.

만약 ```@Transactional``` 어노테이션을 주석 처리하고 다시 '회원가입()' 부분 테스트를 실행하면 잘 되는데, 다시 테스트를 실행하면 에러가 발생한다.

<img width="1723" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/bbbf8dd0-75f0-4c93-bec4-76cf7c1adfea">

왜냐하면 DB에 "spring"이 들어있기 때문이다.

이 경우 ```@AfterEach``` ~~ 코드를 추가할 수도 있지만, 이렇게 하면 번잡해진다. 스프링이 기가 막힌 것을 제공한다.

데이터베이스는 기본적으로 <b>transaction</b>이라는 개념이 있다. 그래서 DB에 데이타 insert query를 한 다음, commit을 해줘야 DB에 반영이 된다. 그게 아니면 auto-commit인 자동 커밋이 되든지 한다. 즉, 기본적으로 transaction이라는 개념이 있어서 commit을 하기 전까지는 반영이 안 되는 것이다.

<b>테스트가 끝난 후 rollback을 하는 것이다.</b> rollback을 하면 DB에서 테스트 도중에 생성된 데이터가 반영이 안 되고 없어지는 것이다. 그게 바로 ```@Transactional```어노테이션을 테스트 케이스에 붙이는 것이다. 이 어노테이션이 붙으면 테스트가 실행될 때 transaction을 먼저 실행하고 DB에 data를 insert query를 하여 다 넣은 후에, 테스트가 끝나면 rollback을 해준다. 결과적으로 DB에 넣었던 데이터가 반영이 안 되고 다 깔끔하게 지워지는 것이다.

이제 다시 h2에 들어가서 ```delete from member```를 실행하여 데이터베이스를 정리하고, ```@Transactional``` 어노테이션 주석을 해제한 후 '회원가입()'을 재실행한다. 그리고 다시 h2로 가서 데이터가 남아있는지 보면 아래와 같이 아무것도 없다.

<img width="721" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7a675a60-404f-4d1f-89f3-60698752244c">

재실행 한 후 다시 실행해도 정상적으로 작동하는 것을 볼 수 있다.

전체를 돌려도 잘 작동한다.

<br>

정리하면 다음과 같다.

* ```@SpringBootTest```: 스프링 컨테이너와 테스트를 함께 실행한다.
* ```@Transactional```: 테스트 케이스에 이 어노테이션이 있으면, 테스트 시작 전에 트랜잭션을 시작하고, 테스트 완료 후에 항상 롤백한다. 이렇게 하면 DB에 데이터가 남지 않으므로 다음 테스트에 영향을 주지 않는다.

참고로 ```@Transactional``` 어노테이션이 Service에 붙으면 롤백하지 않고 정상적으로 돌고, 테스트 케이스에 붙었을 때에만 롤백하도록 동작한다.

<br>

여기까지 하면 의문이 들 수 있다. 스프링 없이 하는 테스트인 MemberServiceTest 같은 것은 필요 없는 것이 아닐까 하는 의문이다.

<b>MemberServiceTest</b>를 전체적으로 실행해보면 아래와 같이 매우 빨리 진행된다.

<img width="1282" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/71c94a7e-d556-4f5e-81f9-c5ae78554f11">

반면, <b>MemberServiceIntegrationTest</b>를 전체적으로 실행하면 상대적으로 오랜 시간이 걸리는 것을 볼 수 있다.

<img width="1682" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0dac9e39-ee55-494f-a4c1-ddad34857fcf">

스프링 뜨는 데에만 한 세월이고, 만약 테스트 케이스가 많아지면 완전 다른 차원의 속도 차이가 된다. 

이렇게 순수하게 자바 코드로 하면서 최소한의 단위로 하는 것을 '단위 테스트'라 하고, 스프링 컨테이너에 DB까지 연동해서 하는 테스트를 보통 '통합 테스트'라고 한다. 가급적이면 순수한 단위 테스트가 훨씬 좋은 테스트일 확률이 높다. 단위 단위로 쪼개서 테스트를 잘 할 수 있도록 하고, 스프링 컨테이너 없이 테스트할 수 있도록 훈련해야 한다. 이런 테스트가 좋은 테스트일 확률이 높다.