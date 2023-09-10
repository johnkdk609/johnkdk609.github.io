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

<br><br>

먼저 비즈니스 요구사항을 정리하고, 그 다음에 회원 도메인과 회원 도메인 객체를 저장하고 불러올 수 있는 저장소인 리포지토리 객체를 만들 것이다.

그리고 회원 리포지토리가 정상 동작하는지 테스트 케이스를 만들 것이고, 실제 비즈니스 로직이 있는 회원 서비스를 만들고 이것이 정상적으로 동작하는지 테스트를 만들 것이다. 여기서 테스트는 JUnit이라는 테스트 프레임워크로 만들 것이다.


## 비즈니스 요구사항 정리

* 데이터: 회원ID, 이름
* 기능: 회원 등록, 조회
* 아직 데이터 저장소가 선정되지 않음(가상의 시나리오)

<br>

비즈니스 요구사항은 가장 단순한 것으로 만들 것이다. 단순한 예제를 가지고 스프링 생태계 전반적으로 어떤 식으로 개발이 이뤄지고 동작하는지 알아보는 것이 목표이기 때문이다.

추기로 아직 데이터 저장소가 선정되지 않았다는 가상의 시나리오를 상정할 것이다. 개발자는 개발을 해야 하는데 아직 DB가 선정이 안 된 것이다. 성능이 중요한 데이터베이스를 선택할지, 일반적인 관계형 데이터베이스를 선택할지, 아니면 NoSQL로 할지 등이 안 정해진 것이다.


## 일반적인 웹 어플리케이션 계층 구조

<img width="872" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/25c3120d-45d5-4c13-9901-735eee00d581">

* <b>컨트롤러</b>: 웹 MVC의 컨트롤러 역할
* <b>서비스</b>: 핵심 비즈니스 로직 구현
* <b>리포지토리</b>: 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
* <b>도메인</b>: 비즈니스 도메인 객체 ex) 회원, 주문, 쿠폰 등등 주로 데이터베이스에 저장하고 관리됨

<br>

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

<br>

우선 회원 비즈니스 로직에 <b>MemberService</b>가 있고, 회원을 저장하는 <b>MemberRepository(회원 리포지토리)</b>는 인터페이스로 설계할 것이다. 왜냐하면 아직 데이터 저장소가 선정되지 않았기 때문이다. 그래서 구현체를 우선 메모리 구현체<b>(MemoryMemberRepository)</b>로 만들 것이다. 일단 개발은 해야 하니 메모리로 단순하게 저장하는 방식을 사용하는 것이다. 향후에 RDB로 할지, JPA로 할지 등등이 선정되었다면 바꿔 끼울 것이다. 그래서 인터페이스가 필요한 것이다.


## 회원 도메인과 리포지토리 만들기

이제 실제로 코딩을 해볼 것이다.

먼저 회원의 경우, hello.hellospring 아래에 'domain' 패키지를 만들 것이다. 그리고 그 안에 Member라는 클래스를 만들 것이다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2cd60fe7-738f-4840-9cae-bd04280ca1c7)

Member클래스에는 앞서 계획한 요구사항들을 넣어야 한다. 아이디 식별자가 있어야 하고, 이름이 있어야 한다. id는 고객이 정하는 아이디가 아니라 데이터의 구분을 위해 시스템이 저장하는 아이디이다. 이름은 그냥 이름이다.

Getter, Setter도 함께 만들어야 한다.

<br>

<b>Member클래스</b>의 코드는 다음과 같다.

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

<br><br>

다음에는 회원 리포지토리 인터페이스를 만들 것이다. hello.hellospring 아래에 먼저 repository라는 패키지를 만들고, 인터페이스로 MemberRepository를 생성한다. 리포지토리란 저장소를 뜻하는데, 회원 객체를 저장하는 저장소를 만들 것이다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c0eb4333-e1b1-42e0-9084-3b8764481b3b)

<br>

<b>MemberRepository인터페이스</b>의 코드는 다음과 같다.

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

<br>

맨 처음에 기능을 save를 만들 것이다. 회원을 저장하면 저장된 회원이 반환된다.

그리고 Optional을 이용해서 findById 즉 아이디로 회원을 찾는 기능을 만들 것이다. 

findByName도 이름으로 회원을 찾는 기능이다.

findAll은 지금까지 저장된 모든 회원의 리스트를 반환하는 것이다.

Optional은 Java 8에 들어간 기능이다. findById, findByName로 가져올 때 이게 null일 수 있다. 없으면 null이 반환될 것이다. 요즘에는 null을 그대로 반환하는 것 대신에 Optional로 감싸서 반환하는 것을 선호한다.

<br><br>

이제 구현체를 만들 것이다. repository 패키지 아래에 MemoryMemberRepository라는 클래스를 만든다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/319f74ce-e5a0-49ef-a687-510719407957)

<br>

<b>MemoryMemberRepository클래스</b>의 코드는 다음과 같다.

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

먼저 save를 할 때 메모리이니 저장을 어딘가에 해둬야 한다. 그래서 Map을 쓸 것이다. key는 회원의 아이디니까 Long으로 하고 값은 Member로 할 것이다. 그리고 store이라 하고 ```new HashMap<>();```을 할 것이다. 그리고 sequence도 만들 것이다. sequence는 0, 1, 2, ... 이렇게 key값을 생성해주는 것이다. (실무에서는 동시성 문제 때문에 좀 더 고려해야할 것들이 있다.)

이제 member을 save할 때, setId를 할 때 sequence값을 하나 올려줄 것이다. 그리고 ```store.put(member.getId(), member);``` 로 해서 넣고, member를 리턴한다. store에 넣기 전에 member의 id값을 세팅해주고 이름은 save하기 전에 넘어온 상태라고 보면 된다. Member클래스를 보면 id, name이 있는데 name은 고객이 회원가입할 때 적는 이름이고 id는 시스템이 정해주는 것이다. 그리고 저장된 결과를 반환(return member)해주는 것이다.

findById의 경우 store에서 꺼내면 된다. ```return store.get(id);```로 해도 된다. 그런데 이 결과가 없으면 null이 반환될 것이다. 과거에는 그냥 이렇게 했는데, 요즘에는 null이 반환될 가능성이 있으면 Optional이라는 것으로 감싼다. ```return Optional.ofNullable(store.get(id));```로 수정하면 null이어도 감쌀 수 있다. 이렇게 감싸서 반환을 해주면 클라이언트에서 무언가 할 수 있다. (뒤에서 설명할 것이다.)

findByName의 경우 람다를 이용할 것이다. ```.filter(member -> member.getName().equals(name))```으로 member.getName()이 파라미터로 넘어온 name과 같은지 확인한다. 같은 경우에만 필터링이 되고, 그 중에서 찾으면 반환을 한다. ```.findAny()```는 하나로도 찾는 것이다. 루프를 돌면서 하나 찾아지면 그냥 반환하고, 끝까지 돌렸는데 없으면 Optional에 null이 포함돼서 반환된다.

findAll은 간단하다. ```return new ArrayList<>(store.values());```를 통해 윗줄에 ```private static Map<Long, Member> store = new HashMap<>();```의 Member들이 쭉 반환되는 것이다.

이렇게 구현이 끝났다.


## 회원 리포지토리 테스트 케이스 작성

회원 리포지토리 테스트 케이스를 작성해볼 것이다. 방금 만든 회원 리포지토리 클래스가 내가 원하는 대로 정상적으로 동작할지 검증하는 방법이 있다. 이때 테스트 케이스를 작성해서 검증한다.

개발한 기능을 실행해서 테스트할 때 자바의 main 메서드를 통해서 실행하거나, 웹 애플리케이션의 컨트롤러를 통해서 해당 기능을 실행한다. 이러한 방법은 준비하고 실행하는 데 오래 걸리고, 반복 실행하기 어렵고 여러 테스트를 한 번에 실행하기 어렵다는 단점이 있다.

자바는 <b>JUnit</b>이라는 프레임워크로 테스트를 실행해서 이러한 문제를 해결한다.

<br>

먼저, src/test/java 하위에 respository라는 패키지를 만든다. 그리고 그 안에 MemoryMemberRepositoryTest클래스를 생성한다. 

<img width="348" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7db7956f-787c-4e3f-8ceb-2b654d983c3b">

<br>

<b>MemoryMemberRepositoryTest클래스</b>의 코드는 다음과 같다.

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.Test;

import java.util.List;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;

class MemoryMemberRepositoryTest {

    MemoryMemberRepository repository = new MemoryMemberRepository();

    @AfterEach
    public void afterEach() {
        repository.clearStore();
    }

    @Test
    public void save() {
        Member member = new Member();
        member.setName("spring");

        repository.save(member);

        Member result = repository.findById(member.getId()).get();
        assertThat(result).isEqualTo(member);
    }

    @Test
    public void findByName() {
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("spring2");
        repository.save(member2);

        Member result = repository.findByName("spring1").get();

        assertThat(result).isEqualTo(member1);
    }

    @Test
    public void findAll() {
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("spring2");
        repository.save(member2);

        List<Member> result = repository.findAll();

        assertThat(result.size()).isEqualTo(2);
    }

}
```

일단 ```MemoryMemberRepository repository = new MemoryMemberRepository();```를 입력한다.

그리고 이전에 만들었던 <b>save</b>기능이 동작하는지 테스트해볼 것이다. ```@Test```라는 어노테이션을 붙이면 테스트를 실행할 수 있다. member를 생성하고, ```repository.save(member);```로 저장한다. 그리고 반환 타입이 Optional이므로 ```Member result = repository.findById(member.getId()).get();```을 입력한다.

이제 검증을 해볼 것인데, new 해서 저장한 것과 내가 DB에서 꺼낸 것이 똑같으면 참이다. 여기서 org.junit.jupiter.api가 제공하는 ```Assertions.assertEquals(member, result);```를 이용한다. 이때 앞의 member는 expected 즉 기대하는 것이고 뒤의 result는 actual 즉 실제 값이다.

이제 실행을 해보면 녹색불이 뜬다.

<img width="1221" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ba497b91-b38e-44d1-aac9-b0ed2145d1ba">

만약 실제 값인 actual을 null로 변경하고 다시 실행하면 다음과 같이 빨간색이 뜬다. 오류가 난 것이다. 

<img width="1505" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/98313382-ef7a-4498-a80f-c33f8402ab47">

```
org.opentest4j.AssertionFailedError: 
Expected :hello.hellospring.domain.Member@2002fc1d
Actual   :null
```

Expected와 Actual이 다르다는 에러 메세지가 나타나는 것을 알 수 있다.

<br>

요즘에는 ort.assertj.core.api가 제공하는 Assertions.assertThat() 문법을 더 많이 사용한다. 좀 더 편하게 쓸 수 있다. ```Assertions.assertThat(member).isEqualTo(result);```로 좀 더 직관적으로 작성할 수 있는 것이다. ```option + enter```를 누르고 Assertions를 static import하면 위와 같이 Assertions없이 작성할 수 있다.

다시 실행해보면 정상적으로 작동한다.

<img width="1223" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1380ac0b-c810-4b41-9157-bb7a6561c199">

실무에서는 이것을 빌드 툴과 엮어서 빌드할 때 테스트 케이스를 통과하지 않으면 다음 단계로 못 넘어가게 막아버린다.

<br>

그 다음에 테스트해야 하는 것은 <b>findByName</b>이다. 이름으로 찾는 메서드이다.

member1을 생성해서 이름을 "spring1"로 repository에 저장하고, member2를 생성해서 이름을 "spring2"로 repository에 저장한다.

```Optional<Member> result = repository.findByName("spring1").get();```를 입력해 spring1을 찾는다. 이제 ```assertThat(result).isEqualTo(member1);```을 통해 같은지 확인한다.

실행하면 정상적으로 작동한다. 그런데 ```Member result = repository.findByName("spring2").get();```로 변경하면 이제 result는 member2이다. 실행하면 빨간불이 뜬다. 다른 객체가 뜬다는 것이다.

<img width="1555" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2e0b6144-26c7-4b6c-a19a-a9a806e302fa">

<br>

이번에는 <b>findAll</b>를 테스트해볼 것이다.

마찬가지로 member1을 생성해서 이름을 "spring1"로 repository에 저장하고, member2를 생성해서 이름을 "spring2"로 repository에 저장한다.

그 다음에 ```List<Member> result = repository.findAll();```를 입력해 result를 만든다. 그리고 ```assertThat(result.size()).isEqualTo(2);```를 입력해 사이즈가 맞는지 확인한다. 실행하면 녹색불이 뜬다. 만약 isEqualTo(3)으로 변경하면 에러가 뜬다.

<br>

그런데 여기서 개별 메서드를 테스트하는 것이 아닌, 전체 테스트를 돌리면 findByName에서 에러가 발생한다.

<img width="1558" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/300aba28-f768-4bfc-8d90-1f254b77d0e9">

개별적으로는 문제 없이 동작했는데 전체 테스트를 돌리면 에러가 발생하는 것이다.

테스트 순서는 보장이 안 된다. 모든 테스트는 순서와 상관 없이 메서드 별로 따로 동작하도록 설계해야 한다. (순서에 의존적으로 설계하면 안 된다.)

위 사진을 보면 findAll()이 먼저 실행이 됐다. 이때 "spring1", "spring2"가 저장이 이미 된 것이다. 그래서 findByName()을 테스트할 때 다른 객체가 이전에 저장했던 "spring1"이 나와버리는 것이다.

그래서 테스트가 하나 끝나면 이 데이터를 깔끔하게 clear해줘야 한다. 테스트가 끝날 때마다 repository를 깔끔하게 지워주는 코드를 넣어야 하는 것이다.

어노테이션 ```@AfterEach```는 어떤 메서드가 실행이 끝날 때마다 어떤 동작을 하게끔 하는 콜백(callback) 메서드이다. save()가 끝나고, findByName()이 끝나고, findAll()이 끝날 때마다 호출이 되게 하는 것이다.

MemoryMemberRepository클래스로 돌아가서 아래에 clearStore라는 메서드를 생성한다.

```java
public void clearStore() {
    store.clear();
}
```

clear()로 store를 싹 비우는 것이다. 다시 MemeoryMemberRepositoryTest클래스로 가서 afterEach() 메서드에 ```repository.clearStore();```를 입력한다. 이제 전체 테스트를 실행하면 녹색불이 뜬다.

<img width="1216" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7cee68ee-3ef6-4e5b-8eeb-f0e7427df19e">

<br>

<b>테스트는 서로 순서와 관계 없이, 의존 관계 없이 설계되어야 한다.</b> 그러기 위해서는 하나의 테스트가 끝날 때마다 저장소가 공용 데이터들을 깔끔하게 지워야 한다.

<br>

이렇게 MemoryMemberRepository 개발을 먼저 한 후에 테스트 클래스를 작성하는 경우도 있지만, 반대로 테스트 클래스를 먼저 작성하고 MemoryMemberRepository를 작성할 수도 있다. 순서를 뒤집는 것이다.

이런 방식을 <b>TDD(테스트 주도 개발)</b>이라고 한다. 무언가를 만들어야 할 때, 틀을 미리 만드는 것이다. 지금 내가 사용한 방식은 구현 클래스를 먼저 만들고 테스트를 만든 것이기 때문에 TDD는 아니다.

테스트가 수십~수백개일 경우, 

<img width="450" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/37e76cec-80c7-4100-be7c-d52adc97ecca">

이렇게 클릭하여 테스트를 한꺼번에 자동으로 돌릴 수 있다.

테스트 코드가 없이 개발하는 것은 나 혼자 개발할 때는 가능할 수도 있지만, 정말 많은 사람들이 개발하고 소스코드가 몇만~몇십만 라인을 넘어가면 테스트 코드 없이 개발하는 것이 불가능하다.


## 회원 서비스 개발

이제 회원 서비스 클래스를 만들 것이다. 회원 서비스는 회원 리포지토리라는 도메인을 활용해서 실제 비즈니스 로직을 작성하는 부분이다.

src/main/java/hello.hellospring 아래에 service라는 패키지를 만들고, 그 안에 MemberService클래스를 생성한다.

<img width="273" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/384ae416-eefb-4b6c-8e2b-d334ef5eb6d9">

<br>

<b>MemberService클래스</b>의 코드는 다음과 같다.

```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;

import java.util.List;
import java.util.Optional;

public class MemberService {

    private final MemberRepository memberRepository;

    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    /**
     * 회원가입
     */
    public Long join(Member member) {
        validateDuplicateMember(member);    // 중복 회원 검증
        memberRepository.save(member);
        return member.getId();
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
        return memberRepository.findAll();
    }

    public Optional<Member> findOne(Long memberId) {
        return memberRepository.findById(memberId);
    }

}
```

일단 회원 서비스를 만들려면 회원 리포지토리가 있어야 한다. 그래서 ```private final MemberRepository memberRepository = new MemoryMemberRepository();```를 입력한다.

그 다음에 <b>회원가입(join)</b>부터 만든다. 회원 가입은 memberRepository에 save만 호출하면 된다. 그리고 임의로 아이디를 반환하게 했다. 

회원가입을 할 때 비즈니스 로직 중에 같은 이름이 있는 중복 회원이 안 된다고 했었다. 이것을 하려면 ```Optional<Member> result = memberRepository.findByName(member.getName());```으로 먼저 찾아본다. 이때 Optional로 반환이 됐다. 그러면 ```result.ifPresent(m -> { throw new IllegalException("이미 존재하는 회원입니다.") })``` 즉 null이 아니라 어떤 값이 있으면 동작하게 하는 것이다. Optional로 한 번 감싸면 Optional 안에 member가 있는 것이다. Optional에 여러 메서드가 있기 때문에 그것을 쓸 수 있다. (ifPresent 같은 메서드도 Optional 안에 있는 것들이다.)

추가적으로 Optional에는 orElseGet() 메서드가 있는데, 값이 있으면 꺼내고 없으면 어떤 메서드를 실행하거나 default값을 꺼내도록 할 수 있다. 

Optional을 사용할 때, Optional을 바로 반환하는 것이 (보기에) 별로 좋지 않다. 어차피 result가 반환됐기 때문에 바로 ifPresent가 들어갈 수 있다.

```java
memberRepository.findByName(member.getName())
    .ifPresent(m -> {
        throw new IllegalStateException("이미 존재하는 파일입니다.");
    });
```

이렇게 수정하는 것이다. findByName을 하고 그 결과는 Optional member이니 바로 Optional member.ifPresent로 작성하는 것이다.

여기서 findByName이라는 로직이 쭉 나온다. 이런 경우에는 메서드로 뽑는 것이 좋다. findByName이 사용된 부분을 마우스로 드래그 하고 단축키 ```ctrl + T```를 누르면 된다. 이 단축키를 누르면 리팩토링과 관련된 여러 가지 것들이 나온다. Extract Method를 누르고 이름을 'validateDuplicateMember'라고 입력하고 생성한다.

결론적으로, "join을 하면 중복 회원 검증하고 통과하면 저장하는구나"하고 이해할 수 있는 것이다.

<br>

이제 <b>전체 회원 조회(findMembers)</b> 기능을 만들 것이다.

앞서 작성했던 MemberRepository는 save, findById, findByName, findAll처럼 단순히 저장소에 넣었다 뺐다 하는 느낌이 있다. 그런데 서비스 클래스는 join, findMembers 등.. 네이밍이 좀 더 비즈니스에 가깝다. 보통 서비스 클래스는 비즈니스에 가까운 용어를 사용해야 한다. 그래야 개발자든 기획자든 소통이 잘 되고 매칭이 잘 되는 것이다. <b>서비스는 비즈니스에 의존적으로 설계하고 리포지토리는 서비스보다는 기계적으로, 개발스럽게 용어를 선택한다.</b>

전체 회원 조회는 ```memberRepository.findAll()```을 하면 된다. 반환 타입은 List이다.

<br>

한 가지만 더 한다면, <b>findOne</b>을 만들어보겠다.

memberId를 넘겨서 ```return memberRepository.findById(memberId);```를 입력하여 Optional로 감싸져 있는 Member객체를 돌려받는다.


## 회원 서비스 테스트

이번에는 회원 서비스 클래스를 테스트해볼 것이다. MemberService클래스에 들어가서, ```cmd + shift + T```를 누르면 'Create New Test...'라고 뜬다. Testing library는 JUnit5로 선택하고, Class name은 'MemberServiceTest'로 그대로 둔다. 그리고 하단의 Member는 모두 선택하고 OK를 눌러 생성한다.

그러면 test/java/hello.hellospring.service 패키지 아래에 MemberServiceTest클래스가 생성된다. 그 안에 join, findMembers, findOne 모두 껍데기가 만들어져 있다.

Test의 경우 메서드명을 과감하게 한글로 변경해도 된다. 가령 'join'을 '회원가입'으로 명명해도 되는 것이다. 그리고 빌드 될 때 테스트 코드는 실제 코드에 포함되지 않는다.

<br>

<b>MemberServiceTest클래스</b>의 코드는 다음과 같다.

```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemoryMemberRepository;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;
import static org.junit.jupiter.api.Assertions.*;

class MemberServiceTest {

    MemberService memberService;
    MemoryMemberRepository memberRepository;

    @BeforeEach
    public void beforeEach() {
        memberRepository = new MemoryMemberRepository();
        memberService = new MemberService(memberRepository);
    }

    @AfterEach
    public void afterEach() {
        memberRepository.clearStore();
    }

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
/*
        try {
            memberService.join(member2);
            fail();
        } catch (IllegalStateException e) {
            assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");
        }
*/

        // then

    }


    @Test
    void findMembers() {

    }

    @Test
    void findOne() {
    }
}
```

우선 서비스가 있어야 하기 때문에, ```MemberServic memberService = new MemberService();```를 입력해 memberService를 생성한다.

<br>

<b>회원가입</b> 메서드를 테스트할 것이다. 그런데, given, when, then문법을 사용할 것이다.

> **given** - 어떠한 상황이 주어졌고/
> **when** - 이것을 실행했을 때/
> **then** - 결과가 이것이 나와야 한다.

마치 머리, 가슴, 배처럼 테스트는 보통 given, when, then으로 잘린다. 상황에 따라서 안 맞을 경우 변경을 하면 된다.

우선 member를 생성하고 "hello"를 이름으로 세팅한다. 그리고 memberService에 join을 검증하는 것이기 때문에, ```Long saveId = memberService.join(member);```를 입력해 member객체를 넣는다.

이제 then에서 검증을 해야 한다. Assertions를 입력하고 'org.assertj.core.api'를 클릭해 import한다.

내가 저장한 것이 리포지토리에 있는 것이 맞는지 알고 싶다. 그래서 ```memberService.findOne(saveId).get();```를 findMember에 담는다. 그리고 ```Assertions.assertThat(member.getName()).isEqualTo(findMember.getName());```을 입력해 member의 이름이 findMember의 이름과 같은지 알아본다.

이제 실행하면 녹색불이 뜬다.

<img width="1233" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f648c272-e0fc-4071-9eb6-bc4fa0418bc0">

그런데 사실 테스트는 정상 flow도 중요하지만 예외 flow가 훨씬 더 중요하다. join의 핵심은 저장이 되는 것도 중요하지만, 중복 회원 검증 로직을 잘 파서 예외가 발생하는지를 봐야 한다.

<br>

이제 <b>중복_회원_예외</b> 메서드를 테스트해볼 것이다.

given부분에 member1을 생성하고 "spring"이라는 이름으로 세팅한다. 그리고 member2를 생성하고 또 이름을 "spring"으로 세팅한다.

이제 when부분에 join을 두 번 한다. ```memberService.join(member1);```, ```memberService.join(member2);```를 입력한다. 이름이 "spring"으로 동일하기 때문에, MemberService의 validateDuplicateMember에 걸린다. 그래서 예외가 발생한다.

<img width="717" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/079a371c-6c59-479c-8898-92aeb8e92d54">

이 예외가 발생한 것을 try-catch문으로 잡을 수도 있다. memberService에 member2를 저장할 경우 fail하고, catch문으로 가서 IllegalStateException이 발생했을 경우 ```assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");```를 통해 예외 메세지가 동일한지 보는 것이다. 만약 "이미 존재하는 회원입니다." 부분을 조금만 수정해도 테스트는 실패한다.

그런데 이것 때문에 try-catch문을 넣는 것이 애매하다. 좋은 문법이 따로 있다. 우선 생성한 try-catch문을 주석처리한다.

when부분에 assertThrows를 입력한다. ```assertThrows(IllegalStateException.class, () -> memberService.join(member2));```를 입력한다. IllegalStateException이 발생하는 것을 기대하는 것인데, 오른쪽에 memberService.join(member2)의 로직을 태울 때 발생하는 것이다. 이제 테스트를 실행하면 성공한다.

만약 IllegalStateException을 가령 NullPointerException으로 변경하면 테스트는 실패한다.

메세지를 검증하기 위해서는 이것을 반환해야 한다. ```cmd + option + V```를 입력하고 이름을 'e'로 설정한다. e에 받아서 이것을 검증하는 것이다.

이제 ```assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");```를 입력하고 테스트를 돌리면 된다.

<br>

만약에 앞서 테스트했던 '회원가입' 메서드에서 setName을 "spring"으로 했으면 한꺼번에 테스트를 돌릴 때 오류가 발생할 것이다. clear를 해줘야 하는데, 문제가 있다. clear를 해주고 싶은데 memberService밖에 없는 것이다. clear가 안 된다.

그래서 ```MemoryMemberRepository memberRepository = new MemoryMemberRepository();```를 입력한다. 그리고 memberRepository를 clear해줘야 한다.

```java
@AfterEach
public void afterEach() {
    memberRepository.clearStore();
}
```

위 코드를 입력해 각 테스트가 끝나고 나면 DB의 값을 날려준다. (```ctrl + R```을 누르면 이전에 실행했던 것을 그대로 실행해준다.)

<br>

그런데 조금 애매한 것이 있다. Test에서 하는 MemoryMemberRepository는 MemberService에 있는 memberRepository는 다른 객체이다. 이것을 두 개를 쓸 이유가 없고, 같은 것을 쓰는 것이 아무래도 낫다.

MemoryMemberRepository에서 static으로 되어 있다. static은 인스턴스와 상관 없이 클래스 레벨에서 붙는 것이기 때문에 사실 크게 상관은 없지만, 그래도 new로 다른 객체의 리포지토리가 생성이 되면 다른 인스턴스이기 때문에 무언가 내용이 달라질 수도 있다.

그래서 같은 인스턴스를 쓰게 바꿔야 한다. MemberService에서 new ~ 를 지운다.

```java
private final MemberRepository memberRepository;

public MemberService(MemberRepository memberRepository) {
    this.memberRepository = memberRepository;
}
```

이렇게 수정한다. memberRepository를 직접 new 해서 생성하는 것이 아니라 외부에서 넣어주도록 바꾸는 것이다.

이제 MemberServiceTest클래스에 다음과 같은 코드를 수정 및 삽입한다.

```java
MemberService memberService;
MemoryMemberRepository memberRepository;

@BeforeEach
public void beforeEach() {
    memberRepository = new MemoryMemberRepository();
    memberService = new MemberService(memberRepository);
}
```

이렇게 하면 테스트를 실행할 때마다 각각 생성을 해준다. ```@beforeEach``` 어노테이션은 각 테스트를 실행하기 전에 실행된다는 것이다. MemoryMemberRepository를 만들고 위에 memberRepository에 넣어놓는다.

이것을 MemberService 입장에서 보면 직접 new로 생성하지 않고 외부에서 넣어준다. 이것을 <b>DI(Dependency Injection, 의존성 주입)</b>라고 한다.