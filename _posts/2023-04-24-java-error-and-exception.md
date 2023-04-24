---
layout: post
title: 프로그램 에러(error)와 예외(exception)
description: Java의 정석 기초편 ch. 8 예외처리
categories: Java
date: 2023-04-24 15:54:00 +09:00
---
프로그램이 실행 중 어떤 원인에 의해서 오작동을 하거나 비정상적으로 종료되는 경우가 있다. 이러한 결과를 초래하는 원인을 프로그램 에러 또는 오류라고 한다.

이를 발생 시점에 따라 '컴파일 에러(compile-time error)'와 '런타임 에러(runtime error)'로 나눌 수 있는데, 글자 그대로 '컴파일 에러'는 컴파일할 때 발생하는 에러이고 '런타임 에러'는 프로그램의 실행 도중에 발생하는 에러이다.

이 외에도 '논리적 에러(logical error)'가 있는데, 컴파일도 잘 되고 실행도 잘 되지만 의도한 것과 다르게 동작하는 것을 말한다. 예를 들어, 창고의 재고가 음수가 된다든가, 게임 프로그램에서 비행기가 총에 맞아도 죽지 않는 경우가 이에 해당한다.

> **컴파일 에러** - 컴파일 시에 발생하는 에러\
> **런타임 에러** - 실행 시에 발생하는 에러\
> **논리적 에러** - 실행은 되지만, 의도와 다르게 동작하는 것

소스코드를 컴파일 하면 컴파일러가 소스코드(\*.java)에 대해 오타나 잘못된 구문, 자료형 체크 등의 기본적인 검사를 수행하여 오류가 있는지 알려준다. 컴파일러가 알려준 에러들을 모두 수정해서 컴파일을 성공적으로 마치고 나면, 클래스 파일(\*.class)이 생성되고, 생성된 클래스 파일을 실행할 수 있다. 

하지만 컴파일을 에러 없이 성공적으로 마쳤다고 해서 프로그램의 실행 시에도 에러가 발생하지 않는 것은 아니다. 컴파일러가 실행 도중에 발생할 수 있는 잠재적인 오류까지 검사할 수 없기 때문에 컴파일은 잘 되었어도 실행 중에 에러에 의해서 잘못된 결과를 얻거나 프로그램이 비정상적으로 종료될 수 있다.

런타임 에러를 방지하기 위해서는 프로그램의 실행 도중 발생할 수 있는 모든 경우의 수를 고려하여 이에 대한 대비를 하는 것이 필요하다. 자바에서는 실행 시(runtime) 발생할 수 있는 프로그램 오류를 '에러(error)'와 '예외(exception)' 두 가지로 구분하였다.

> **에러**(error) - 프로그램 코드에 의해서 수습될 수 없는 심각한 오류\
> **예외**(exception) - 프로그램 코드에 의해서 수습될 수 있는 다소 미약한 오류

에러는 메모리 부족(OutOfMemoryError)이나 스택오버플로우(StackOverFlowError)와 같이 일단 발생하면 복구할 수 없는 심각한 오류이고, 예외는 발생하더라도 수습될 수 있는 비교적 덜 심각한 것이다.


### 예외 클래스의 계층구조

자바에서는 실행 시 발생할 수 있는 오류(Exception & Error)를 클래스로 정의하였다. 모든 클래스의 조상은 Object 클래스이므로 Exception이나 Error 클래스 역시 Object 클래스의 자손들이다.

<img width="800" alt="예외클래스 계층도" title="예외클래스 계층도 출처:https://velog.io/@mingseok/예외-클래스의-계층구조" src="https://user-images.githubusercontent.com/88493727/233925156-12c7f13b-ef55-4307-94ce-ab7edef42a26.png">

위 그림은 전체 Exception 클래스 중에서 몇 개의 주요 클래스들만을 나열한 것이다.

모든 예외의 최고 조상은 Exception 클래스이며, 상속계층도를 Exception 클래스부터 도식화 하면 다음과 같다.

<img width="684" alt="Exception클래스와 Runtime클래스 중심의 상속계층도" title="Exception클래스와 Runtime클래스 중심의 상속계층도 출처:https://velog.io/@mingseok/예외-클래스의-계층구조" src="https://user-images.githubusercontent.com/88493727/233926596-a91e9668-f1a9-423b-ae50-abb3a98c5c1c.png">

위 그림에서 볼 수 있듯이 예외 클래스들은 다음과 같이 두 그룹으로 나눠질 수 있다.

> 1. Exception 클래스와 그 자손들 (바로 위 그림의 윗부분 음영. RuntimeException과 자손들 제외)
> 2. RuntimeException 클래스와 그 자손들 (바로 위 그림의 아랫부분 음영)


### Exception과 RuntimeException

앞으로 RuntimeException 클래스와 그 자손 클래스들을 'RuntimeException 클래스들'이라 하고, RuntimeException 클래스들을 제외한 나머지 클래스들을 'Exception 클래스들'이라 하겠다.

> **Exception 클래스들** - 사용자의 실수와 같은 외적인 요인에 의해 발생하는 예외\
> **RuntimeException 클래스들** - 프로그래머의 실수로 발생하는 예외

RuntimeException 클래스들은 주로 프로그래머의 실수에 의해서 발생될 수 있는 예외들로 자바의 프로그래밍 요소들과 관계가 깊다. 

예를 들면, 배열의 범위를 벗어난다든가(ArrayIndexOutOfBoundsException), 값이 null인 참조변수의 멤버를 호출하려 했다든가(NullPointerException), 클래스 간의 형변환을 잘못했다든가(ClassCastException), 정수를 0으로 나누려고(ArithmeticException)하는 경우 등에 발생한다. 

Exception 클래스들은 주로 외부의 영향으로 발생할 수 있는 것들로서, 프로그램 사용자들의 동작에 의해서 발생하는 경우가 많다. 

예를 들면, 존재하지 않는 파일의 이름을 입력했다든가(FileNotFoundException), 실수로 클래스의 이름을 잘못 적었다든가(ClassNotFoundException), 또는 입력한 데이터 형식이 잘못된(DataFormatException) 경우에 발생한다.
