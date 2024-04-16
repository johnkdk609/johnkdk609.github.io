---
layout: post
title: JSTL (JSP Standard Tag Library)
categories: Spring-SpringBoot
date: 2024-04-16 14:16:00 +0900
---
이제 <b>JSTL</b>에 대해 알아보겠다.

## JSTL (JSP Standard Tag Library)

* JavaEE 기반의 웹 어플리케이션 개발을 위한 컴포넌트 모음
* JSP 스크립트와 html 코드가 섞여서 복잡한 구조를 가짐. 이를 간결하게 작성하기 위해서 자바 코드를 태그 형태로 작성해 놓은 것
* 유용한 커스텀 태그들을 모아서 표준화 한 것

지금까지 HTML 코드 안에 Java 코드를 넣었는데, 생각보다 지저분하다고 생각하는 사람이 많다. 이것을 없애기 위해 EL을 사용했다. 그런데 EL을 통해서 반복문을 돌리는 등의 방법이 없었으니, JSTL을 통해서 반복문, 조건문을 다루고 Java 냄새를 최대한 뺄 것이다.

자바 코드를 태그 형태로 작성해놓은 것이니, 사실 JSTL만 잘 익혀놨다면 자바 코드를 모르더라도 JSP를 만들 수 있다.

<br>

## JSTL 기능

JSTL의 기능들로는 다음과 같은 것이 있다.

* 간단한 프로그램 로직 구현 기능 - 변수 선언, if 문장, for 문 등
* 데이터 출력 포맷 설정
* DB 입력, 수정, 삭제, 조회 기능
* 문자열 처리 함수
* XML 문서 처리

(그런데 기능들 중 간단한 프로그램 로직 구현 기능 정도만 실질적으로 사용하고, 나머지 기능들은 더 유용한 다른 기술들로 대체할 것이다.)

<br>

## JSTL 사용하기

JSTL을 사용하기 위해서는 jar 파일 혹은 Maven 프로젝트로 변경시켜야 한다.

> Jakarta Standard Tag Library API >> 3.0.0
> Jakarta Standard Tag Library Implementation >> 3.0.1

위 두 개를 넣어야 사용할 수 있다. mvnrepository에서 "jstl"이라고 검색하고 가져온다.

외부에서 받은 jar 파일을 추가하려면 프로젝트에 마우스 우클릭을 하고, Build Path > Configure Build Path > Libraries > Classpath 에서 Add JARs 버튼을 클릭하여 집어넣을 수 있다. 

<br>

Dynamic Web Project에서 jar 파일을 추가하는 것은 훨씬 더 간단해졌다. WEB-INF 폴더 안에는 라이브러리들을 모아놓는 lib 폴더가 있다. 이 lib 폴더 안에 다운로드 받아놓은 jar 파일들을 복사해서 넣으면 된다. 

이 방법이 싫다면 Maven 프로젝트로 변경 후 pom.xml 파일에 코드를 추가하면 된다.

<br>

JSTL을 사용하려면 우선 taglib 지시자를 이용해 태그 사용 선언을 해야 한다.

```jsp
<% taglib prefix="c" uri="jakarta.tags.core" %>
```

* 사용하고자 하는 기능에 따라 어떤 라이브러리를 사용할지 작성한다. (ex: core)
* 사용할 태그를 구분하기 위해서 prefix를 작성해준다.

```jsp
<c:out value="Hello! JSTL" />
```

* prefix에 작성한 접두사를 적어주고, 기능에 따른 태그를 선택하여 작성한다.