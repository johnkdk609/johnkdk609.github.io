---
layout: post
title: 스프링 DB 접근 기술 - 순수 JDBC
description: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
categories: Spring-SpringBoot
date: 2023-09-21 11:56:00 +0900
---
이번 시간에는 애플리케이션에서 DB에 연동해서 저장하는 것을 이전처럼 메모리에 저장하는 것이 아닌, 데이터베이스에 insert query를 날리고 select query를 날려서 DB에 넣고 빼는 것을 한 번 해볼 것이다. 그것도 가장 오래 된 20여년 전 방식을 사용해볼 것이다.

먼저 <b>build.gradle</b>파일에 jdbc, h2 데이터베이스 관련 라이브러리를 추가해야 한다.

<img width="806" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0ae5efcb-6d4e-4427-9aa3-c90631eb09f4">

위 그림과 같이 두 줄을 추가한다. 'spring-boot-starter-jdbc', 즉 jdbc라는 것을 하는 것이라고 생각하면 된다. 자바는 기본적으로 DB와 붙으려면 이 jdbc 드라이버가 꼭 있어야 한다. 이것을 가지고 서로 연동을 하는 것이다. 'h2database:h2'는 DB랑 붙을 때 데이터베이스가 제공하는 클라이언트가 필요한데, 이 문장이 클라이언트이다.

<br>

DB에 붙으려면 접속 정보를 넣어야 한다. 옛날에는 개발자가 일일이 설정했는데, 요즘에는 스프링 부트가 다 해준다. 그래서 경로만 넣으면 된다.

src/main/resources/application.properties 안에 다음과 같이 추가한다. 

```
spring.datasource.url=jdbc:h2:tcp://localhost/~/test
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
```

그리고 build.gradle에 가서 refresh 한다. 원래 이 부분에 id, password를 적는데 h2 데이터베이스는 이것을 생략해도 된다.

이렇게 하면 데이터베이스에 접근하기 위한 준비는 다 되었다. 이렇게 하면 스프링이 DB와 연결하는 작업을 다 해준다.

<br>

이제 JDBC API를 가지고 개발을 해보겠다.

기존에는 MemoryMemberRepository를 만들었었다. 이것을 인터페이스를 만들었기 때문에 구현체를 만들면 된다. 우선 src/main/java/repository 안에 JdbcMemberRepository클래스를 만든다. 

<b>회원을 저장하는 역할은 MemberRepository가 하지만, 구현을 메모리에 할 것인지, DB와 연동해서 JDBC로 할 것인지의 차이가 있는 것이다.</b>

<b>JdbcMemberRepository클래스</b>의 코드는 다음과 같다. (복사 붙여넣기 하였다.)

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import org.springframework.jdbc.datasource.DataSourceUtils;

import javax.sql.DataSource;
import java.sql.*;
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

public class JdbcMemberRepository implements MemberRepository {

    private final DataSource dataSource;

    public JdbcMemberRepository(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    @Override
    public Member save(Member member) {
        String sql = "insert into member(name) values(?)";

        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;

        try {
            conn = getConnection();
            pstmt = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);

            pstmt.setString(1, member.getName());

            pstmt.executeUpdate();
            rs = pstmt.getGeneratedKeys();

            if (rs.next()) {
                member.setId(rs.getLong(1));
            } else {
                throw new SQLException("id 조회 실패");
            }
            return member;
        } catch (Exception e) {
            throw new IllegalStateException(e);
        } finally {
            close(conn, pstmt, rs);
        }
    }

    @Override
    public Optional<Member> findById(Long id) {
        String sql = "select * from member where id = ?";

        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;

        try {
            conn = getConnection();
            pstmt = conn.prepareStatement(sql);
            pstmt.setLong(1, id);

            rs = pstmt.executeQuery();

            if(rs.next()) {
                Member member = new Member();
                member.setId(rs.getLong("id"));
                member.setName(rs.getString("name"));
                return Optional.of(member);
            } else {
                return Optional.empty();
            }

        } catch (Exception e) {
            throw new IllegalStateException(e);
        } finally {
            close(conn, pstmt, rs);
        }
    }

    @Override
    public List<Member> findAll() {
        String sql = "select * from member";

        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;

        try {
            conn = getConnection();
            pstmt = conn.prepareStatement(sql);

            rs = pstmt.executeQuery();

            List<Member> members = new ArrayList<>();
            while(rs.next()) {
                Member member = new Member();
                member.setId(rs.getLong("id"));
                member.setName(rs.getString("name"));
                members.add(member);
            }

            return members;
        } catch (Exception e) {
            throw new IllegalStateException(e);
        } finally {
            close(conn, pstmt, rs);
        }
    }

    @Override
    public Optional<Member> findByName(String name) {
        String sql = "select * from member where name = ?";

        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;

        try {
            conn = getConnection();
            pstmt = conn.prepareStatement(sql);
            pstmt.setString(1, name);

            rs = pstmt.executeQuery();

            if(rs.next()) {
                Member member = new Member();
                member.setId(rs.getLong("id"));
                member.setName(rs.getString("name"));
                return Optional.of(member);
            }
            return Optional.empty();
        } catch (Exception e) {
            throw new IllegalStateException(e);
        } finally {
            close(conn, pstmt, rs);
        }
    }


    private Connection getConnection() {
        return DataSourceUtils.getConnection(dataSource);
    }
    private void close(Connection conn, PreparedStatement pstmt, ResultSet rs)
    {
        try {
            if (rs != null) {
                rs.close();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        try {
            if (pstmt != null) {
                pstmt.close();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        try {
            if (conn != null) {
                close(conn);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private void close(Connection conn) throws SQLException {
        DataSourceUtils.releaseConnection(conn, dataSource);
    }
}
```

우선, DB에 붙으려면 DataSource가 필요하다. 그래서 ```private final DataSource dataSource```라고 기입하는 것이다. 그리고 나중에 스프링한테 주입을 받아야 한다. 이전에 application.properties에 세팅을 해뒀는데, 그러면 스프링 부트가 dataSource를 만들어 놓는다.

save의 ResultSet은 결과를 받는 것이다. 먼저 getConnection()을 해서 커넥션을 가져오고, prepareStatement(sql, Statement.RETURN_GENERATED_KEYS)을 기입해서 DB에 insert를 해야 1번, 2번, ... 이렇게 얻어올 수 있었는데, 이것을 얻어오는 것이다.

그 다음에 setString(1, member.getName())을 기입해 parameterIndex가 1번으로 뜨면, 위 문장의 ?와 매칭이 되고 여기에 member.getName()으로 값을 넣는다.

그리고 executeUpdate를 하여 DB의 실제 query가 날라간다. 그리고 getGenerateKeys를 하여 DB에서 나온 key값(1, 2, ...)을 꺼내준다. 그리고 rs(result set)에 rs.next를 하여 값이 있으면 그 값을 꺼내고 세팅해주면 된다.

그리고 쓴 자원들을 release 해줘야 한다. DB 커넥션 같은 경우 쓰면 외부 네트워크로 연결되어 있기 때문에 끝나면 바로 전원을 끊어야 한다. 안 그러면 큰일 난다. DB 커넥션이 계속 쌓이다가 장애가 발생할 수 있는 것이다.

findById의 경우 우선 select 쿼리를 날려서 가져온다. 그리고 커넥션을 가지고 sql을 날리고 prepareStatement을 세팅한 다음에, executeQuery로 rs을 받아 와서 만약에 값이 있으면 멤버 객체를 쭉 만들고 반환을 해준다.

findAll도 마찬가지이다. 이번에는 List 컬렉션에 담아서 루프 돌리면서 collection.add 해서 멤버를 쭉 담고 반환한다.

findByName도 마찬가지이다.

(참고: 맨 위에서 ```dataSource.getConnection();```을 기입할 수도 있지만, 이렇게 하면 계속 새로운 커넥션이 주어지게 된다. 스프링 프레임워크를 통해서 data connection을 쓸 때에는 DataSourceUtils를 통해서 커넥션을 획득해야 한다.)

닫을 때에도 커넥션은 DataSourceUtils를 통해서 release 해줘야 한다.

<br>

이제 configuration을 해야 한다. 여태까지 했던 것은 전부 메모리에서 한 것이었다. src/main/java/hello.hellospring/service 아래에 있는 SpringConfig를 보면 MemoryMemberRepository를 스프링 빈으로 등록하고 있다. 이것을 방금 생성한 JdbcMemberRepository로 바꾼다. 그리고 위에 ```private DataSource dataSource;```를 기입하고, Contructor를 생성한다. 스프링 부트가 dataSource를 보고 자체적으로 빈을 생성해준다. 데이터베이스와 연결할 수 있는 정보가 있는 dataSource를 만들어주고 주입을 해주는 것이다.

수정한 <b>SpringConfig클래스</b>의 코드는 다음과 같다.

```java
package hello.hellospring;

import hello.hellospring.repository.JdbcMemberRepository;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;

@Configuration
public class SpringConfig {

    private final DataSource dataSource;

    @Autowired
    public SpringConfig(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
//        return new MemoryMemberRepository();
        return new JdbcMemberRepository(dataSource);
    }
}
```

<br>

지금까지 보면 다른 어떤 코드도 변경하지 않았다. 오직 JdbcMemberRepository클래스를 만들고, 인터페이스를 확장했다. 스프링이 제공하는 ```@Configuration```만 손 댔다.

이제 돌려보겠다. (h2 데이터베이스는 꼭 실행해두어야 한다.)

웹 브라우저에 가서 localhost:8080으로 들어간 다음 회원 목록을 보면 정상적으로 작동한다.

회원 가입 버튼을 누르고 'jpa'라는 이름으로 다시 등록하면 다음과 같이 출력된다.

<img width="351" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7cece17a-4869-4f5b-a30e-b53f5db59b3a">

이렇게 해서 데이터베이스에 접근하는 것이 굉장히 잘 동작하고 있는 것을 확인할 수 있다.