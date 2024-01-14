---
layout: post
title: 컴포넌트 스캔
description: 스프링 핵심 원리 - 기본편
categories: Spring-SpringBoot
date: 2024-01-04 23:27:00 +0900
---
이번 시간부터는 컴포넌트 스캔에 대해 알아볼 것이다. 컴포넌트 스캔과 의존관계 자동 주입이 연결된 부분이 있다.

## 목차

1. 컴포넌스 스캔과 의존관계 자동 주입 시작하기
2. 탐색 위치와 기본 스캔 대상
3. 필터
4. 중복 등록과 충돌

## 1. 컴포넌트 스캔과 의존관계 자동 주입 시작하기

지금까지 스프링 빈을 등록할 때에는, 자바 코드의 ```@Bean```, XML의 ```<bean>``` 등을 통해서 설정 정보에 직접 등록할 스프링 빈을 나열했다. 예제에서는 몇 개가 안 되었지만, 이렇게 등록해야 할 스프링 빈이 수십, 수백개가 되면 일일이 등록하기도 귀찮고, 설정 정보도 커지고, 누락하는 문제도 발생한다. 역시 개발자는 반복을 싫어한다. (무엇보다 귀찮다.)

그래서 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 컴포넌트 스캔이라는 기능을 제공한다.

또 의존관계도 자동으로 주입하는 ```@Autowired```라는 기능도 제공한다.

<br>

코드로 컴포넌트 스캔과 의존관계 자동 주입을 알아보자.

먼저 기존 AppConfig.java는 과거 코드와 테스트를 유지하기 위해 남겨두고, 새로운 AppConfig.java를 만들어보겠다. hello.core 패키지 안에 AutoAppConfig클래스를 생성한다. <b>AutoAppConfig클래스</b>의 코드는 다음과 같다.

```java
package hello.core;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;

@Configuration
@ComponentScan(
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {

}
```

우선 여기까지 코드를 작성했다. 우선 설정 정보이니 ```@Configuration```을 적는다. 컴포넌트 스캔은 스프링 빈을 긁어서 자동으로 스프링 빈으로 끌어올려야 한다. 그래서 ```@ComponentScan```이라는 어노테이션을 쓴다. 이 컴포넌트 스캔은 ```@ComponentAnnotation```이 붙은 클래스를 찾아서 자동으로 스프링 빈으로 등록해준다. 그런데 먼저, 괄호를 열어서 ```excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)```를 입력한다. 이 코드는 컴포넌트 스캔으로 다 뒤져서 스프링 빈으로 자동 등록하는데, 그 중에서 뺄 것을 지정해주는 것이다. 타입을 지정하고, ```Configuration.class```를 뺄 것이다. 이 어노테이션이 붙은 것은 뺄 것이라는 것이다.

AppConfig는 자동으로 등록되면 안 된다. 지금 수동으로 등록하는데 이러면 충돌이 날 것이다. ```@Configuration``` 어노테이션에 ```Alt```를 누른 채 클릭해서 들어가보면, ```@Component```라는 것이 붙어있다. 

<img width="785" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/57895d9d-6ba9-4298-842e-cb8052528168">

그래서 얘도 자동 스캔의 대상이 되어버린다. 그래서 일단은 예제를 안전하게 유지하기 위해서 exclude를 한 것이다.

<br>

여기까지 한 것을 정리하면 다음과 같다.

* 컴포넌트 스캔을 사용하려면 먼저 ```@ComponentScan```을 설정 정보에 붙여주면 된다.
* 기존의 AppConfig와는 다르게 ```@Bean```으로 등록한 클래스가 하나도 없다!

(참고: 컴포넌트 스캔을 사용하면, ```@Configuration```이 붙은 설정 정보도 자동으로 등록되기 때문에, AppConfig, TestConfig 등 앞서 만들어두었던 설정 정보도 함께 등록되고, 실행되어 버린다. 그래서 ```excludeFilters```를 이용해서 설정정보는 컴포넌트 스캔 대상에서 제외했다. 보통 설정 정보를 컴포넌트 스캔 대상에서 제외하지는 않지만, 기존 예제 코드를 최대한 남기고 유지하기 위해서 이 방법을 선택했다.)

<br>

컴포넌트 스캔은 이름 그대로 ```@Component``` 어노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록한다. ```@Component```를 붙여주자.

(참고: ```@Configuration```이 컴포넌트 스캔의 대상이 된 이유도, ```@Configuration``` 소스코드를 열어보면 ```@Component``` 어노테이션이 붙어있기 때문이다.)

<br>

이제 각 클래스가 컴포넌트 스캔의 대상이 되도록 ```@Component``` 어노테이션을 붙여주자.

<br>

우선 MemoryMemberRepository클래스에 ```@Component```를 붙인다. 이제 빈 등록을 안 해도 된다.

<img width="585" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/3a5a30c9-8c15-453f-b441-71e8feef9f4a">

그 다음에 사용하기로 한 RateDiscountPolicy클래스에도 ```@Component```를 붙여준다.

<img width="531" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/392df765-0f85-4967-9359-009d98878495">

그리고 MemberServiceImpl도 스프링 빈으로 등록해야 되니까, ```@Component```를 붙여준다.

<img width="526" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ae9e61e2-08d3-43a8-9bae-2e470c40b794">

<br>

여기까지 했는데, 한 가지를 더 해줘야 한다. MemberServiceImpl의 경우, 내가 스프링 빈으로 등록하는 것이 아니다. 자동으로 스프링 빈으로 등록이 되는 것이다. 그러면 의존관계 주입은 어떻게 할 것인가? 기존에 AppConfig에서는 MemberService는 memberRepository() 의존관계를 주입할 것이라고 딱 명시할 수 있었다.

<img width="583" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/a2f45d81-c90b-42a7-afe8-5f539b5c5a0a">

그런데, AutoAppConfig를 보면, 아무것도 없다. 그냥 MemberServiceImpl이 떡하니 스프링 빈으로 등록되는 것이다. 그러면 의존관계 주입을 해줄 수 있는 방법이 없다.

그래서 자동 의존관계 주입이 필요하다.

<br>

의존관계를 자동으로 주입해주는 ```@Autowired```라는 기능을 생성자에 붙여주면, 스프링이 여기에다가 MemberRepository 타입을 만든 것을 찾아와서 의존관계를 자동으로 주입해서 연결해주는 것이다.

<img width="588" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/46614b1d-c21d-4335-8ef1-6cc8992cb63a">

그래서 컴포넌트 스캔을 쓰게 되면, 내 빈이 자동으로 등록이 되는데 의존관계를 설정할 수 있는 방법이 없다. 왜냐하면 수동으로 등록할 수 있는 장소가 없는 것이다. 그래서 ```@Autowired```를 쓰게 되면 스프링이 ```MemberServiceImpl```도 스프링 빈으로 등록해주고, MemberRepository 타입에 맞는 memoryMemberRepository 스프링 빈을 딱 주입해주는 것이다. 마치 ```ac.getBean(MemberRepository.class)```의 방식으로 동작한다고 이해하면 된다. 자동으로 이 코드가 들어간다고 보면 되는 것이다.

<br>

지금까지 한 것을 정리해보면 다음과 같다.

* 이전에 AppConfig에서는 ```@Bean```으로 직접 설정 정보를 작성했고, 의존관계도 직접 명시했다. 이제는 이런 설정 정보 자체가 없기 때문에, 의존관계 주입이 이 클래스 안에서 해결해야 한다.
* ```@Autowired```는 의존관계를 자동으로 주입해준다. (자세한 룰은 조금 뒤에 나온다.)

<br>

한 개 더 남았다. OrderServiceImpl에도 마찬가지로 위에 ```@Component```를 붙인다. 그러면 컴포넌트 스캔의 대상이 되면서 스프링 빈으로 등록된다. 그리고 생성자 위에 ```@Autowired```를 입력한다. 그러면 스프링이 얘를 생성할 때 자동으로 ApplicationContext에서 MemberRepository도 주입하고, DiscountPolicy도 주입해준다.

<img width="837" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c1680048-16dc-4577-96ea-5b585334aa70">

```@Autowired```를 사용하면 생성자에서 여러 의존관계도 한 번에 주입받을 수 있다.

<br>

이제 테스트 코드를 한 번 짜보겠다. test 쪽에 패키지를 scan이라고 해서 하나 생성한다. 그리고 그 안에 AutoAppConfigTest클래스를 생성한다. AutoAppConfigTest클래스의 코드는 다음과 같다.

```java
package hello.core.scan;

import hello.core.AutoAppConfig;
import hello.core.member.MemberService;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import static org.assertj.core.api.Assertions.*;

public class AutoAppConfigTest {

    @Test
    void basicScan() {
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class);

        MemberService memberService = ac.getBean(MemberService.class);
        assertThat(memberService).isInstanceOf(MemberService.class);
    }
}
```

basicScan이라는 이름으로 만든다. 그리고 AnnotationConfigApplicationContext를 생성해주는데, AutoAppConfig.class를 입력한다. 여기서 ```Alt```를 누른 채 AutoAppConfig를 클릭하면 안에 아무것도 없다. 그냥 ```@ComponentScan``` 어노테이션만 있고, ```exclude```하는 조건만 써 있다.

이렇게 해놓고 조회하면 된다. ```ac.getBean(MemberService.class)```로 MemberService를 조회해본다. 그 다음에 Assertions를 한다. memberService는 MemberService.class의 instanceOf인지 입력하는 것이다.

이제 실행하면 다음과 같다.

<img width="1684" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/07d5f1e6-5551-47f0-869c-c9331850a92e">

AnnotationConfigApplicationContext를 사용하는 것은 기존과 똑같다. 그런데 설정 정보로 ```AutoAppConfig``` 클래스를 넘겨줬다. 실행해보면 기존과 같이 잘 동작하는 것을 확인할 수 있다.

로그를 잘 보면 컴포넌트 스캔이 잘 동작하는 것을 확인할 수 있다.

위 실행 결과를 보면, ClassPathBeanDefinitionScanner이라는 것들이 뜨면서 ```Identified candidate component class:``` 라면서 뭔가 component class의 후보로 식별했다는 것이 뜬다.

그리고 밑에 로그를 보면 ```Creating shared instance of singleton bean```이라면서 이것들이 싱글톤 빈으로 생성됐다고 나온다. 그리고 로그에 Autowired에 대한 정보도 나온다. ```Autowiring by type from bean name 'memberServiceImpl' via constructor to bean named 'memoryMemberRepository'``` 즉, 생성자를 통해서 이것들을 주입했다는 정보가 나오는 것이다.

<br>

컴포넌트 스캔과 자동 의존관계 주입이 어떻게 동작하는지 그림으로 알아보자.

### 1. @ComponentScan

<img width="983" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/cd012dca-5c0c-42f0-93f1-2662490d23f5">

* ```@ComponentScan```은 ```@Component```가 붙은 모든 클래스를 스프링 빈으로 등록한다.
* 이때 스프링 빈의 기본 이름은 클래스명을 사용하되 맨 앞글자만 소문자를 사용한다.
    * <b>빈 이름 기본 전략:</b> MemberServiceImpl 클래스 → memberServiceImpl
    * <b>빈 이름 직접 지정:</b> 만약 스프링 빈의 이름을 직접 지정하고 싶으면 ```@Component("memberService2")``` 의 방식으로 이름을 부여하면 된다.

예를 들어서 MemberServiceImpl클래스에서 ```@Component("memberService2")```라고 수정해두고 AutoAppConfig클래스의 basicScan() 테스트를 실행시키면 다음과 같이 결과가 나온다.

<img width="1685" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5516a416-4fcb-4712-8d1b-cf14434d1ad3">

```Creating shared instance of singleton bean 'memberService2'```라고 나오는 것이다. 이런 식으로 해줄 수도 있다.

웬만하면 default를 쓰면 된다. 특별한 경우에만 이름을 부여하면 된다.

### 2. @Autowired 의존관계 자동 주입

<img width="982" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4b4ab43b-76cf-447e-a088-48eb3f4f4524">

위 그림을 보면, MemberServiceImpl의 생성자에 ```@Autowired```를 입력했다.

* 생성자에 ```@Autowired```를 지정하면, 스프링 컨테이너가 컨테이너에 있는 memberRepository를 뒤진다. 자동으로 해당 스프링 빈을 찾아서 주입한다.
* 이때 기본 조회 전략은 타입이 같은 빈을 찾아서 주입한다.
    * ```getBean(MemberRepository.class)```와 동일하다고 이해하면 된다.

좀 더 자세한 내용에 대해서는 뒤에서 설명할 것이다.

<img width="984" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1d8cd629-7fce-4c33-80a8-aa99280890b7">

그리고 위 그림처럼 OrderServiceImpl을 보면, ```@Autowired``` 해서 MemberRepository도 필요하고 DiscountPolicy도 필요하다. 타입으로 다 찾아서 다 주입해눈다.

생성자에 파라미터가 많아도 다 찾아서 자동으로 주입하는 것이다.

<br>

정리하면 다음과 같다.

AutoAppConfig를 보면, ```@ComponentScan```가 있을 경우 자동으로 클래스 패스를 다 뒤져서 안에 있는 것들을 스프링 빈에 등록해준다. 그런데 그냥 등록하는 것은 아니고 ```@Component```가 붙은 애들을 다 뒤져서 스프링 컨테이너에 자동으로 등록해주는 것이다.

그런데 스프링 컨테이너에 자동으로 등록하다 보니, 의존관계를 주입할 수 있는 방법이 없다. 이전에 수동으로 설정할 때에는 'MemberServiceImpl은 ~~를 의존관계로 주입해' 같은 방식으로 직접 설정 정보에 다 적어줬었다. 그런데 설정 정보 자체를 안 쓰기 때문에 의존관계 자동 주입(```@Autowired```)을 사용해서 주입을 한다. 그러면 얘는 타입인 MemberRepository을 찾아서 등록해준다.

## 2. 탐색 위치와 기본 스캔 대상

이번에는 컴포넌트 스캔의 탐색 위치와 기본 스캔 대상에 대해 알아보겠다.

먼저, 탐색할 패키지의 시작 위치를 지정할 수 있다. ```@ComponentScan```을 적을 때, ```basePackages```를 적을 수 있는 것이다.

```java
package hello.core;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;

@Configuration
@ComponentScan(
        basePackages = "hello.core.member",
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {

}
```

이 경우 ```basePackages = "hello.core.member"```라는 코드를 추가했다. 이것은 member패키지부터 하위 패키지를 찾아가는 것이다.

<img width="268" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c9a9cc1e-545f-43be-93b0-85eea5a18ad3">

이렇게 해버리면 member만 컴포넌트 스캔의 대상이 된다. 기존에 돌렸던 AutoAppConfigTest의 basicScan()을 돌려보면 다음과 같이 실행결과가 나온다.

<img width="1683" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b1362486-413d-4796-a4e6-28fe9564b1b0">

보면 memberServiceImpl과 memoryMemberRepository 딱 두 개만 등록이 됐다. 나머지는 (discountPolicy, order 등에 대한 것) 아예 등록이 안 됐다.

즉, 어디서부터 찾을지 지정해주는 것이다. 시작 위치를 지정하는 것이다.

이게 없으면 모든 자바 코드를 다 뒤져야 한다. 엄청 오래 걸리는 것이다. (라이브러리들까지 다 뒤지면 엄청난 시간이 걸릴 것이다.) 여러 라이브러리가 섞여있을 때, 컴포넌트 스캔을 안 하고 싶을 때도 있을 수 있다. 이럴 때에 유연하게 쓸 수 있는 것이다.

정리하자면 다음과 같다.

* ```basePackages```: 탐색할 패키지의 시작 위치를 지정한다. 이 패키지를 포함해서 하위 패키지를 모두 탐색한다.
    * ```basePackages = {"hello.core", "hello.service"}``` 이렇게 여러 시작 위치를 지정할 수도 있다.
* ```basePackageClasses```: 지정한 클래스의 패키지를 탐색 위치로 지정한다.
* 만약 지정하지 않으면, ```@ComponentScan```이 붙은 설정 정보 클래스의 패키지가 시작 위치가 된다.

### 권장하는 방법

김영한 선생님이 즐겨 사용하시는 방법은 패키지 위치를 지정하지 않고, 설정 정보 클래스의 위치를 프로젝트 최상단에 두는 것이다. 최근 스프링 부트도 이 방법을 기본으로 제공한다.

예를 들어서 프로젝트가 다음과 같이 구조가 되어 있다고 하자.

* ```com.hello```
* ```com.hello.service```
* ```com.hello.repository```

그러면 나의 프로젝트의 최상단은 ```com.hello```이다. (프로젝트 시작 루트) 여기에 AppConfig 같은 메인 설정 정보를 두는 것이다. (지금의 경우 AutoAppConfig 이다.) 그리고 거기에 ```@ComponentScan``` 어노테이션을 붙이고, ```basePackages``` 지정은 생략한다.

그러면 위 세 개를 다 뒤진다. 내 프로젝트와 관련된 것은 자동으로 다 뒤지는 것이다.

이렇게 하면 ```com.hello```를 포함한 하위는 모두 자동으로 컴포넌트 스캔의 대상이 된다. 그리고 프로젝트 메인 설정 정보는 프로젝트를 대표하는 정보이기 때문에 프로젝트 시작 루트 위치에 두는 것이 좋다고 생각한다.

참고로 스프링 부트를 사용하면 스프링 부트의 대표 시작 정보인 ```@SpringBootApplication```를 이 프로젝트 시작 루트 위치에 두는 것이 관례이다. (그리고 이 설정 안에 바로 ```@ComponentScan```이 들어있다!)

<br>

처음에 프로젝트를 자동으로 만들면 main 메서드가 있는 CoreApplication이 자동으로 만들어진다. 스프링 부트를 돌리는 클래스이다.

<img width="520" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/41478059-1a9d-43c4-8080-79267da0f138">

여기서 ```@SpringBootApplication```을 ```Alt```를 누른 채 클릭하고 들어가면 다음과 같다.

<img width="901" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f23d94c0-6121-4b39-a863-196aa9b12523">

```@ComponentScan```이 걸려있는 것이다.

즉, 스프링 부트를 쓰면 내 프로젝트인 ```hello.core```에서부터 컴포넌트 스캔을 하겠다는 것이다. 그래서 스프링 부트를 쓰면 ```@ComponentScan```에 의해서 자동으로 다 스프링 빈이 등록되고 하는 것이다. 그래서 스프링 부트를 쓰면 사실 ```@ComponentScan``` 자체를 쓸 일이 없다. (부트에서 이미 다 해주니까)

그래서 관례를 따라서, 프로젝트 루트에 컴포넌트 스캔을 지정하고 불필요한 몇 가지는 ```exclude``` 해서 쓰는 것을 권장한다.

<br>

### 컴포넌트 스캔 기본 대상

컴포넌트 스캔은 ```@Component``` 뿐만 아니라 다음과 같은 내용도 추가로 컴포넌트 스캔 대상에 포함한다.

* ```@Component```: 컴포넌트 스캔에서 사용
* ```@Controller```: 스프링 MVC 컨트롤러에서 사용
* ```@Service```: 스프링 비즈니스 로직에서 사용
* ```@Repository```: 스프링 데이터 접근 계층에서 사용 (JPA, JDBC 등)
* ```@Configuration```: 스프링 설정 정보에서 사용

그런데 이것들을 붙여놓으면 자동으로 컴포넌트 스캔의 대상이 된다. 

가령 ```@Service```를 보기 위해 인텔리제이에서 <kbd>Cmd + O</kbd>를 클릭하고, '@Service'를 입력한 다음 클릭해 들어가면, 다음과 같이 볼 수 있다.

<img width="759" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/82a9e996-7cad-4439-a99b-0b83082e0cc8">

보면 위에 ```package org.springframework.stereotype;```라고 되어 있다. 아래에 ```@interface```라고 되어 있으면 바로 뒤의 ```Service```가 어노테이션이다. 보면 ```@Component```가 붙어있다. ```@Repository```도 마찬가지이다.

해당 클래스의 소스 코드를 보면 ```@Component```를 포함하고 있는 것을 알 수 있다.

```java
@Component
public @interface Controller {
}

@Component
public @interface Service {
}

@Component
public @interface Configuration {
}
```

(참고: 사실 어노테이션에는 상속관계라는 것이 없다. 그래서 이렇게 어노테이션이 특정 어노테이션을 들고 있는 것을 인식할 수 있는 것은 자바 언어가 지원하는 기능은 아니고, 스프링이 지원하는 기능이다.)

<br>

컴포넌스 스캔의 용도뿐만 아니라 다음 어노테이션이 있으면 스프링은 부가 기능을 수행한다.

* ```@Controller```: 스프링 MVC 컨트롤러로 인식
* ```@Repository```: 스프링 데이터 접근 계층으로 인식하고, 데이터 계층의 예외를 스프링 예외로 변환해준다.
* ```@Configuration```: 앞서 보았듯이 스프링 설정 정보로 인식하고, 스프링 빈이 싱글톤을 유지하도록 추가 처리를 한다.
* ```@Service```: 사실 ```@Service```는 특별한 처리를 하지 않는다. 대신 개발자들이 핵심 비즈니스 로직이 여기에 있겠구나 라고 비즈니스 계층을 인식하는 데 도움이 된다.

(참고: ```useDefaultFilters``` 옵션은 기본으로 켜져있는데, 이 옵션을 끄면 기본 스캔 대상들이 제외된다. 그냥 이런 옵션이 있구나 정도 알고 넘어가면 되겠다.)


## 3. 필터

이번에는 필터에 대해 알아보겠다.

* ```includeFilters```: 컴포넌트 스캔 대상을 추가로 지정한다.
* ```excludeFilters```: 컴포넌트 스캔에서 제외할 대상을 지정한다.

<br>

먼저, 테스트의 scan 패키지 안에 filter 패키지를 만든다. 그리고 어노테이션을 만들어볼 것이다. filter 패키지 안에 이름을 MyIncludeComponent를 입력하고 IntelliJ에서 Annotation을 선택한다.

<b>MyIncludeComponent 어노테이션</b>의 코드는 다음과 같다.

```java
package hello.core.scan.filter;

import java.lang.annotation.*;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyIncludeComponent {
}
```

위 코드에서 ```@Target(Element.TYPE)```가 중요하다. TYPE이라고 하면 클래스 레벨에 붙는 것이다.

이 어노테이션이 붙은 것은 컴포넌트 스캔에 추가할 것이라고 이해하면 된다.

이제 이 코드를 복사 붙여넣기 해서 이름을 MyExcludeComponent로 붙일 것이다. 어노테이션 2개를 만든 것이고, MyExcludeComponent 어노테이션이 붙으면 컴포넌트 스캔에서 제외할 것이다.

```java
package hello.core.scan.filter;

import java.lang.annotation.*;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyExcludeComponent {
}
```

<br>

이제 filter패키지에 클래스를 두 개 만들 것이다. 하나는 BeanA 클래스이다.

```java
package hello.core.scan.filter;

@MyIncludeComponent
public class BeanA {
}
```

그리고 마찬가지로 복사 붙여넣기 해서 BeanB 클래스를 생성하고, 어노테이션만 ```@MyExcludeComponent```를 붙인다.

```java
package hello.core.scan.filter;

@MyExcludeComponent
public class BeanB {
}
```

<br>

이제 테스트를 하나 만들어볼 것이다. filter패키지 안에 ComponentFilterAppConfigTest클래스를 생성한다.

```java
package hello.core.scan.filter;

import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;

import static org.assertj.core.api.Assertions.*;
import static org.springframework.context.annotation.ComponentScan.*;

public class ComponentFilterAppConfigTest {

    @Test
    void filterScan() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(ComponentFilterAppConfig.class);
        BeanA beanA = ac.getBean("beanA", BeanA.class);
        assertThat(beanA).isNotNull();

        ac.getBean("beanB", BeanB.class);
    }

    @Configuration
    @ComponentScan(
            includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
            excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
    )
    static class ComponentFilterAppConfig {
    }
}
```

```@Test``` 어노테이션을 붙이고, filterScan()을 입력한다. 먼저 아래에 ComponentFilterAppConfig를 static class로 하나 만든다. 우선 ```@Configuration``` 어노테이션을 붙인다. 그리고 ```@ComponentScan``` 어노테이션을 붙이고, 위와 같이 includeFilters와 excludeFilters를 입력한다. 타입은 ```FilterType.ANNOTATION```을 하고, 클래스는 위와 같이 한다. 이렇게 하면 나만의 컴포넌트를 스캔할 수 있는 기능이 만들어진다.

그 다음에 ```new AnnotationConfigApplicationContext(ComponentFilterAppConfig.class);```을 입력하고, ```ApplicationContext ac```에 담아준다.

beanA를 생성하고, Assertions를 이용해 ```assertThat(beanA).isNotNull();```을 검토한다. (static import를 사용했다.) 이제 beanB를 조회하면, 이 경우 조회하는 순간 Exception이 터져야 한다. 왜냐하면 없기 때문이다.

실행 결과는 다음과 같다.

<img width="1682" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0345f115-ea9b-4990-97b5-a82bf5b1c540">

'No bean named 'beanB' available'라고 나온다. 왜냐하면 beanB는 MyExcludeComponent로 해버렸기 때문이다. 그래서 컴포넌트 스캔 대상에서 빠져버린 것이다.

<br>

위 테스트를 완료하려면, 다음과 같이 코드를 수정해야 한다.

```java
package hello.core.scan.filter;

import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoSuchBeanDefinitionException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;

import static org.assertj.core.api.Assertions.*;
import static org.junit.jupiter.api.Assertions.*;
import static org.springframework.context.annotation.ComponentScan.*;

public class ComponentFilterAppConfigTest {

    @Test
    void filterScan() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(ComponentFilterAppConfig.class);
        BeanA beanA = ac.getBean("beanA", BeanA.class);
        assertThat(beanA).isNotNull();

        assertThrows(
                NoSuchBeanDefinitionException.class,
                () -> ac.getBean("beanB", BeanB.class));
    }

    @Configuration
    @ComponentScan(
            includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
            excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
    )
    static class ComponentFilterAppConfig {
    }
}
```

우선 Assertions를 junit 것으로 넣는다. 그리고 예외인 ```NoSuchBeanDefinitionException.class```을 입력한다. 그 다음에 ```() -> ac.getBean("beanB", BeanB.class)```을 넣는다. 그리고 static import 하여 깔끔하게 변환한다.

이제 테스트가 성공한다.

<img width="1685" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/3fa42726-ddd8-4881-ba4b-2b904063fec7">

첫 번째 거는 (beanA) 값이 조회가 되어야 하고, 두 번째 거는 (beanB) NoSuchBeanDefinitionException이 나와야 한다.