---
layout: post
title: 자바란?
description: 김영한의 자바 입문 - 코드로 시작하는 자바 첫걸음
categories: Java
date: 2023-12-09 23:20:00 +0900
---
자바란 무엇인지 알아보겠다.

## 자바 표준 스펙

자바는 표준 스펙과 구현 2가지로 나누어져 있다.

<img width="989" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e6666c7b-7593-481c-bb75-ec1da38da86a">

위 그림에서, 위에 있는 부분이 <b>자바 표준 스펙</b>이다. '자바는 이렇게 만들어야 한다'는 설계도이며 문서이다. 이 표준 스펙을 기반으로 여러 회사에서 실제 작동하는 자바를 만든다. 자바 표준 스펙에는 대표적으로 '어떻게 컴파일러를 만들어야 할지', '~한 라이브러리가 있어야 한다든지', '자바를 실행할 때 자바 가상 머신(JVM)은 어떻게 만들어야 하는지' 등의 스펙을 제시한다. 이 표준 스펙은 자바 커뮤니티 프로세스(JCP)를 통해서 관리된다.

이 표준 스펙을 기반으로 여러 회사에서 자기들의 입맛에 맞는 자바를 만든다. 그런데 이 표준 스펙을 무조건 준수해야 한다.

그렇게 등장한 자바들이 오라클 Open JDK, Adoptium Eclipse Temurin, Amazon Corretto 등이 있다. 즉, 여러 회사에서 자바 표준 스펙에 맞추어 실제 작동하는 자바 프로그램을 개발하는 것이다.

각각은 장단점이 있다. (완전히 똑같다면 하나만 있으면 된다.) 실제 동작하는 것은 똑같은데, 최적화의 부분 등에서 약간 차이가 있다. 가령, Amazon Corretto는 AWS에서 잘 작동하도록 최적화가 되어 있다. 만약 내가 AWS를 쓴다면 Amazon Corretto를 설치하는 것이 제일 좋다.

각 회사들은 대부분 여기에다가 추가로 윈도우, MAC, 리눅스 같이 다양한 OS에서 작동하는 버전의 자바도 함께 제공한다. (Oracle Open JDK의 경우 윈도우 버전, MAC 버전, 리눅스 버전이 같이 제공된다는 것이다.)

<br>

(참고: 다양한 자바 구현에 대해서는 다음 사이트를 참고하면 된다. https://whichjdk.com/ko)

<br>

이렇게 해두면 이러한 장점이 있다.

### 변경의 용이

<img width="1004" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1871bb56-5896-4347-93ae-4cf58897c5d4">

자바 구현들은 모두 표준 스펙에 맞도록 개발되어 있다. 따라서 오라클 Open JDK를 사용하다가 Amazon Corretto 자바로 변경해도 대부분 문제 없이 동작한다.

<br>

## 컴파일과 실행

<img width="990" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/eebdcb48-7236-4bf5-8dec-3ff103708ff1">

자바 프로그램은 컴파일과 실행 단계를 거친다.

* 개발자가 ```Hello.java```와 같은 자바 소스코드를 작성한다.
* 자바 컴파일러를 제공해서 소스 코드를 <u>컴파일</u> 한다.
    * 자바가 제공하는 ```javac```라는 프로그램을 사용한다.
    * ```.java``` → ```.class``` 파일이 생성된다. (변환 과정 = 컴파일)
    * 자바 소스 코드를 바이트코드로 변환하며 자바 가상 머신(JVM)에서 더 빠르게 실행될 수 있게 최적화하고 문법 오류도 검출한다.
* 자바 프로그램을 실행한다.
    * 자바가 제공하는 ```java```라는 프로그램을 사용한다.
    * 자바 가상 머신(JVM)이 실행되면서 프로그램이 작동한다.

## IDE와 자바

### 인텔리제이를 통한 자바 설치 관리

<img width="957" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/8907fc4d-27e0-4d51-a55a-f218360f2365">

인텔리제이는 내부에 자바를 편리하게 설치하고 관리할 수 있는 기능을 제공한다. 이 기능을 사용하면 인텔리제이를 통해 자바를 편리하게 다운로드 받고 실행할 수 있다.

여기서 OpenJDK Java도 설치할 수 있고, Adoptium Java도 설치할 수 있다.

### 인텔리제이를 통한 자바 컴파일, 실행 과정

<img width="983" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c77ecf18-45b2-476b-ae7e-354e41d18b68">

* 컴파일
    * 자바 코드를 컴파일 하려면 ```javac```라는 프로그램을 직접 사용해야 하는데, 인텔리제이는 자바 코드를 실행할 때 이 과정을 자동으로 처리해준다. (Ex. ```javac Hello.java```)
    * 인텔리제이 화면에서 프로젝트에 있는 ```out``` 폴더에 가보면 컴파일된 ```.class```파일이 있는 것을 확인할 수 있다.

가령, CommentJava를 열어보면 다음과 같다.

<img width="1167" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ec8dc8a6-9104-4aae-81fe-d0cf1e1babb1">

보면 'CommentJava.class'라고 되어 있다. 컴파일된 것이다. 그냥 소스코드처럼 보이는 것은, 인텔리제이가 Decompiled라고 해서 컴파일된 소스코드를 역으로 바꾼 것이다. 실제로는 바이트 코드로 되어 있어서 사람이 읽을 수 없다.

<img width="741" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/fb076a03-a0eb-4695-84c8-351666b6c213">

무슨 차이가 있냐면, 소스코드 때에는 주석이 있는데, 컴파일 되면 주석이 다 사라진다. Decompile된 내용에는 주석이 다 없어지는 것이다. 컴파일 단계에서는 필요 없는 것들은 다 지우고 나름의 최적화를 한다.