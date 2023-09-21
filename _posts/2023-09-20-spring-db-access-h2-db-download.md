---
layout: post
title: 스프링 DB 접근 기술 - H2 데이터베이스 설치
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-09-20 21:28:00 +0900
---
이제 스프링 DB 접근 기술에 대해 알아볼 것이다.

이전 시간까지 했던 것은 메모리에 저장을 했다가 서버가 내려가면 메모리에 있던 회원 데이터가 다 사라진다. 실무에서는 데이터베이스에 데이터를 저장하고 관리한다.

<br>

스프링 DB 접근 기술 목차는 다음과 같다.

* <b>H2 데이터베이스 설치</b>
* <b>순수 Jdbc</b>
* <b>스프링 JdbcTemplate</b>
* <b>JPA</b>
* <b>스프링 데이터 JPA</b>

<br>

먼저 아주 가볍고 심플한 H2 데이터베이스를 설치해볼 것이다. 

그 다음 시간에는 DB가 설치돼 있으니 데이터베이스 sql을 갖고 애플리케이션 서버와 DB를 연결할 것이다. 이 연결을 할 때 필요한 기술이 Jdbc라는 것인데, 20여년 전에 사용하던 방법인 순수한 Jdbc로 경험해볼 것이다. 

그리고 순수한 Jdbc로 개발하는 것이 너무 어려우니, 스프링이 중복을 제거해서 JdbcTemplate이라는 기술을 제공한다. 이것을 가지고 애플리케이션에서 데이터베이스 sql을 편리하게 날릴 수 있다.

이것보다 더 혁신적인 방법이 sql조차도 개발자들이 직접 짜는 것이 아닌, JPA라는 기술이 DB에 등록, 수정, 조회하는 쿼리를 다 날려주는 것이다. JPA라는 기술을 쓰면 객체를 바로 DB에 쿼리 없이 저장 및 관리할 수 있다.

JPA도 매우 오래된 기술이다. JPA를 굉장히 편리하게 쓸 수 있도록 한 번 감싼 기술인 '스프링 데이터 JPA'에 대해 알아볼 것이다.

지금까지 해왔던 회원이라는 객체를 이 기술들을 적용해보며 한 단계씩 바꿔볼 것이다.


## H2 데이터베이스 설치

H2 데이터베이스는 교육용으로 굉장히 좋다. 용량도 작고, 가볍고, 웹에 어드민 화면도 제공해준다.

* https://www.h2database.com
* 다운로드 및 설치
* h2 데이터베이스 버전은 스프링 부트 버전에 맞춘다.
* 권한 주기: ```chmod 755 h2.sh``` (윈도우 사용자는 x)
* 실행: ```./h2.sh``` (윈도우 사용자는 h2.bat)
* 데이터베이스 파일 생성 방법
    * ```jdbc:h2:~/test``` (최초 한 번)
    * 홈에서 ```~/test.mv.db``` 파일 생성 확인
    * 이후부터는 ```jdbc:h2:tcp://localhost/~/test``` 이렇게 접속

<br>

터미널에서 h2파일 안쪽으로 들어간 다음, h2/bin에서 ```./h2.sh```를 입력해 h2를 실행시킨다.

<img width="243" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/3b01fa53-5d2f-4c1f-b24f-489438a6c3ef">

그러면 아래와 같이 웹브라우저에 h2가 켜진다.

<img width="659" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/9d51803a-fb0a-4aa1-ad7a-556d183b22be">

터미널에서 실행 중인 h2.sh를 ```ctrl + C```로 끄면 웹 브라우저에 떠 있던 h2도 꺼지게 된다.

<br>

이제 ```jdbc:h2:tcp://localhost/~/test```에 '연결' 버튼을 눌러 접속한 다음, 테이블을 하나 생성할 것이다. 입력창에 다음과 같이 기입하고, ```ctrl + Enter```를 누른다.

```sql
create table member
(
    id bigint generated by default as identity,
    name varchar(255),
    primary key (id)
);
```

그러면 다음과 같이 MEMBER가 생긴 것을 알 수 있다.

<img width="198" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1fe32a45-7060-439c-9062-4373dc99ce10">

이제 입력창에 ```select * from member;```를 입력하면 member를 조회할 수 있다. 만약 직접 기입하기 귀찮다면 입력창이 비어 있을 때 그냥 왼쪽에 'MEMBER'를 클릭하면 동일한 명령어가 뜬다. 그리고 ```ctrl + Enter```를 누른다.

<img width="746" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/034e208e-3203-4e12-bbd7-f084863b137f">

<br>

위에서 기입한 sql 코드를 보면, 먼저 id가 있다. Member클래스를 보면, id가 있었다.

<img width="441" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b30bfbc5-1b12-46c7-bd67-f929f3c5663a">

Java에서는 Long인데, DB에서는 bigint라는 타입이다.

'generated by default as identity'라는 것은, null값 즉 값을 세팅하지 않고 insert 하면 DB가 들어왔을 때 자동으로 id값을 채워주는 것이다.

name은 varchar(255)로 만들어놨고, pk(primary key)는 id로 잡았다. 상당히 단순하게 만들어놓은 것이다.

<br>

이제 insert를 해보겠다. 입력창에 ```insert into member(name) values('spring')```을 입력하고 ```ctrl + Enter```를 누른다. 그러면 아래와 같이 나타난다.

<img width="723" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/78890c64-f826-45e0-9df5-7a3f02306df3">

다시 입력창을 비우고 왼쪽의 MEMBER를 클릭한 다음, 실행 버튼을 누르면 아래와 같이 나타난다.

<img width="202" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/dd371614-6ba1-42af-beea-503ac0bd74a6">

추가로 ```insert into member(name) values('spring2')```를 기입하고 조회하면 다음과 같이 나타난다.

<img width="199" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/54f206f2-5404-45e0-beb5-090b90530895">

ID가 1, 2, ... 이렇게 자동으로 올라가는 것을 알 수 있다. pk값의 생성을 DB보고 알아서 해달라고 넘긴 것이다.

이전에 했던 MemoryMemberRepository에서도 똑같이 해놨다.

<img width="711" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/9421b0be-339e-4c12-bf36-75b860978a20">

sequence라는 것이 save 했을 때 자동으로 증가되도록 설정해둔 것이다.

<br>

이렇게 사용했던 sql의 ddl를 관리하는 것이 좋다. src 바깥에 sql 디렉토리를 만들고, ddl.sql 파일을 생성한 다음, 다음과 같이 저장해둔다.

<img width="1044" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/badb657a-3960-4c63-9d02-9999fcaef182">

이런 식으로 만들어놓고 관리하는 것이다. Git 등을 쓰면 변경 내역이 관리가 되어 파악이 가능하기 때문이다.

<br>

지금까지 웹 콘솔로 들어갔는데, 다음시간부터는 우리가 만든 어플리케이션에서 DB에 접근해서 넣고 빼고 할 것이다.