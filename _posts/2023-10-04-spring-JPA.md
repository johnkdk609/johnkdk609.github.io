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

```@Entity``` 어노테이션을 위에 붙인다. 그러면 이것은 JPA가 관리하는 Entity가 되는 것이다. 그리고 ```@Id```를 붙여서 pk를 매핑해준다. ```GeneratedValue(strategy = GenerationType.IDENTITY)```