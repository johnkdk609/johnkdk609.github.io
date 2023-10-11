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


<br>

## AOP 적용

위와 같은 문제를 해결하는 기술을 AOP라고 한다. Aspect Oriented Programming으로 '관점 지향 프로그래밍'이라고 번역하기도 한다. <b>공통 관심 사항(cross-cutting concern)과 핵심 관심 사항(core concern)을 분리하는 것이 핵심</b>이다.

<br>

이전에 했던 것을 보면, 시간 측정 로직을 모든 메서드에 붙였다.

<img width="871" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/db20f3c0-91b2-478e-a67d-a445b62c21fa">

<br>

그게 아니라, 아래와 같이 시간 측정 로직을 한 군데에 다 모으고, 내가 원하는 곳에 적용하는 것이다. 이것을 가능하게 하는 것이 AOP이다.

<img width="869" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/a068a41d-aed3-4151-906e-58e05ac00cfd">

시간 측정 로직을 TimeTraceAop라는 곳에 딱 모아놓고, 내가 원하는 곳들에 적용하겠다고 지정하는 것이다.

<br>

이제 AOP를 적용하는 것을 코드로 알아보겠다.

main/java/hello.hellospring 아래에 aop라는 패키지를 만들고, 그 안에 TimeTraceAop클래스를 생성한다.

<b>TimeTraceAop클래스</b>의 코드는 다음과 같다.

```java
package hello.hellospring.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class TimeTraceAop {

    @Around("execution(* hello.hellospring..*(..))")
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        System.out.println("START: " + joinPoint.toString());
        try {
            return joinPoint.proceed();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("END: " + joinPoint.toString() + " " + timeMs + "ms");
        }
    }
}
```

```joinPoint.proceed();```를 입력하면 다음 메서드로 진행이 된다. 이것을 result에 담는다. 그리고 그 전에 ```System.currentTimeMillis();```를 입력한다. 그리고 try-finally문을 작성한다.

스프링 빈으로 등록하기 위해서는 ```@Component``` 어노테이션을 붙이면 된다. 만약 이 방식 말고 스프링 빈에 등록하는 방식을 사용하려면, 아래와 같이 SpringConfig에 다음과 같이 입력하면 된다.

<img width="342" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/8055b220-15ce-4709-a014-749460282ecb">

(이번에는 그냥 SpringConfig의 추가한 부분은 주석 처리하고, ```@Component```를 사용하겠다.)

그리고 ```@Around()``` 어노테이션을 입력해 이것을 내가 어디에 적용할 것인지 타겟팅을 할 수 있다. ```@Around("execution(* hello.hellospring..*(..))")```를 입력해서 hello.hellospring패키지 하위에는 다 적용하도록 지시할 수 있다.

<br>

이제 서버를 띄워보겠다. 그리고 localhost:8080을 웹 브라우저에 입력해 들어간 다음, '회원 목록' 버튼을 누르면 IntelliJ 콘솔 창에 다음과 같이 나타난다.

<img width="1688" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/46f8d160-bff6-4a28-9643-cfb8cf860be6">

START가 controller, service, repository에 딱 들어가는 것이 보이고, END가 DB에서 조회하는 것, 서비스, 컨트롤러까지 쭉 보인다.

<br>

이제 이전에 각 메서드에 시간 측정하는 로직 붙인 것을 다 빼면 된다. 그리고 다시 스프링을 실행한 다음 '회원 목록' 버튼을 누르면 아래와 같이 콘솔에 나타난다.

<img width="1687" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/9673e227-a4bb-4f83-b80d-e5706fdeb93e">

execution이 3개 나오는 것을 볼 수 있다. 이러면 어디서 병목이 있는지 바로 찾을 수 있다.

<br>

결론적으로 해결 방식을 정리하면 다음과 같다.

* 회원가입, 회원 조회 등 핵심 관심 사항과 시간을 측정하는 공통 관심 사항을 분리한다.
* 시간을 측정하는 로직을 별도의 공통 로직으로 만들었다.
* 핵심 관심 사항을 깔끔하게 유지할 수 있다.
* 변경이 필요하면 이 로직만 변경하면 된다.
* 원하는 적용 대상을 선택할 수 있다.

<br>

그렇다면 AOP실제로 어떻게 동작하는 것인지 알아보겠다.

AOP가 동작하는 것에는 여러 가지 방법이 있다. 스프링에서 어떻게 동작하는지 보면 다음과 같다.

우선 AOP 적용 전 의존 관계이다.

<img width="697" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/02f6c3d7-fd0d-441d-b2cb-04f19987836f">

AOP를 적용하기 전에는 위와 같이 컨트롤러에서 서비스 호출할 때 그냥 이렇게 의존 관계가 있고 호출을 했다. helloController가 memberService를 의존하고 있고, helloController에서 메서드 호출하면 memberService의 join 등의 메서드를 호출하는 것이다.

<br>

그런데, AOP를 적용하고 어디에 적용할지 지정하면 아래와 같이 된다.

<img width="697" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0a1ce5c0-7ca2-44e0-bdee-353429da6ea4">

스프링은 AOP가 있으면 가짜 memberService를 만든다. 이것을 '프록시'라고 한다. 스프링 컨테이너에 빈을 등록할 때, 진짜 스프링 빈 말고 가짜 스프링 빈을 앞에 세워둔다. 그리고 가짜 스프링 빈(프록시)이 끝나면(joinPoint.proceed()를 내부적으로 하면) 그때 진짜를 호출해준다. helloController가 호출하는 것은 진짜 memberService가 아니라 프록시라는 기술로 발생하는 가짜 memberService인 것이다.

<br>

실제로 이것을 확인할 수 있다. MemberController에서 memberService가 injection 될 때 로그를 찍어보면 되는 것이다.

아래와 같이 ```System.out.println("memberService = " + memberService.getClass());```를 추가해준다.

<img width="668" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e8f2f900-4096-45c7-be4a-36e20106a598">

그리고 스프링을 실행한 후 콘솔을 보면 다음과 같이 나타난다.

<img width="793" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/afa53438-3ca1-4bda-b717-c1569c5bc78e">

MemberService가 'EnhancerBySpringCGLIB' 되어 있다고 나타난다. 멤버 서비스를 가지고 코드를 조작하는 기술이 사용된 것이다. 스프링 컨테이너가 AOP가 적용되면 '어 너 memberService에 AOP가 적용되어야 하네' 하면서 프록시(가짜)를 통해서 AOP가 다 실행이 되고, joinPoint.proceed()를 하면 그때 진짜 memberService가 호출되는 것이다.

<br>

그래서 AOP 적용 전의 전체 그림은 아래와 같다.

<img width="695" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/3e303d3c-e260-4065-ab71-4d6fdb348225">

그리고 AOP 적용 후 전체 그림은 아래와 같다.

<img width="693" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/cd13c331-0682-4e2f-89ca-a39fd0be0ea9">

<br>

아예 자바에서는 컴파일 타임에 코드를 generate 해서 자바 코드를 위아래로 박아주는 기술도 있다.