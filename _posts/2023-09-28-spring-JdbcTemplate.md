---
layout: post
title: 스프링 DB 접근 기술 - 스프링 JdbcTemplate
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-09-28 11:21:00 +0900
---
이번에는 스프링 JdbcTemplate에 대해서 알아볼 것이다.

우선 설정의 경우 build.gradle에서 기존에 'spring-boot-starter-jdbc'를 설정해뒀는데 그대로 두면 된다.

<img width="749" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/8e037357-f933-4c0e-97b9-82416b65c94b">

스프링 JdbcTemplate은 MyBatis와 비슷한 라이브러리인데, JDBC API에서 본 반복 코드를 대부분 제거해준다. 하지만 SQL은 직접 작성해야 한다.

(Jdbc Template는 실무에서도 많이 쓴다.)

<br>

main/java/hello.hellospring/repository 안에 JdbcTemplateMemberRepository클래스를 생성한다.

<b>JdbcTemplateMemberRepository클래스</b>의 코드는 다음과 같다.

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.jdbc.core.namedparam.MapSqlParameterSource;
import org.springframework.jdbc.core.simple.SimpleJdbcInsert;

import javax.sql.DataSource;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Optional;

public class JdbcTemplateMemberRepository implements MemberRepository {

    private final JdbcTemplate jdbcTemplate;

    public JdbcTemplateMemberRepository(DataSource dataSource) {
        this.jdbcTemplate = new JdbcTemplate(dataSource);
    }

    @Override
    public Member save(Member member) {
        SimpleJdbcInsert jdbcInsert = new SimpleJdbcInsert(jdbcTemplate);
        jdbcInsert.withTableName("member").usingGeneratedKeyColumns("id");

        Map<String, Object> parameters = new HashMap<>();
        parameters.put("name", member.getName());

        Number key = jdbcInsert.executeAndReturnKey(new MapSqlParameterSource(parameters));
        member.setId(key.longValue());
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        List<Member> result = jdbcTemplate.query("select * from member where id = ?", memberRowMapper(), id);
        return result.stream().findAny();
    }

    @Override
    public Optional<Member> findByName(String name) {
        List<Member> result = jdbcTemplate.query("select * from member where name = ?", memberRowMapper(), name);
        return result.stream().findAny();
    }

    @Override
    public List<Member> findAll() {
        return jdbcTemplate.query("select * from member", memberRowMapper());
    }

    private RowMapper<Member> memberRowMapper() {
        return (rs, rowNum) -> {
            Member member = new Member();
            member.setId(rs.getLong("id"));
            member.setName(rs.getString("name"));
            return member;
        };
    }
}
```

우선 MemberRepository를 implements 한다. 그리고 먼저 JdbcTemplate를 입력한다. 이것은 injection을 받을 수 있는 것은 아니므로, Constructor에서 DataSource를 injection 받는다. 그리고 ```jdbcTemplate = new JdbcTemplate(datasource);```를 입력한다. 그리고 참고로 <b>생성자가 하나만 있으면 스프링 빈으로 등록될 때, ```@Autowired```를 생략해도 된다.</b> 이렇게 하면 스프링이 dataSource를 자동으로 injection 해준다.

먼저 조회하는 query인 findById부터 해보겠다. ```jdbcTemplate.query("select * from member where id = ?", )```를 입력한다. 이때, 결과가 나오는 것을 RowMapper이라는 것으로 매핑을 해야 한다. 코드 아랫부분에 ```private RowMapper<Member> memberRowMapper()```를 입력한다. 해당 코드는 람다로 바꿀 수 있기에 ```option + Enter```를 누르고 'Replace with lambdas'를 클릭해 줄일 수 있다. 이렇게 생성한 memberRowMapper()를 두 번째 파라미터에 넣어주면 된다. 그리고 세 번째 파라미터로 id를 넣는다.

findById를 하면 jdbcTemplate에서 query("select * from member where id = ?")를 날리고, 그 결과를 RowMapper를 통해서 매핑을 하고, 그 다음에 이것을 List로 받아서 Optional로 바꿔서 반환하는 것이다.

<br>

이제 이것을 조립해야 한다. SpringConfig클래스를 열고, 아래와 같이 수정한다.

<img width="593" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/891f4b16-acf6-4322-bf14-c0f85fde4479">

<br>

스프링 통합테스트를 이미 만들어놨기 때문에 웹 어플리케이션을 띄워서 검증할 필요가 없다. 이미 만든 통합테스트를 돌려보면 된다. (DB는 띄워놔야 한다.)

통합테스트를 실행하면 다음과 같이 성공적으로 실행되는 것을 알 수 있다.

<img width="1685" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/cc50b375-eb8f-4fdf-b3a2-ee436f7f7e96">