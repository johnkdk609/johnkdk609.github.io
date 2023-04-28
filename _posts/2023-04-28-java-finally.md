---
layout: post
title: finally블럭
description: Java의 정석 기초편 ch. 8 예외처리
categories: Java
date: 2023-04-28 17:44:00 +09:00
---
**finally블럭**은 예외의 발생 여부에 상관없이 실행되어야할 코드를 포함시킬 목적으로 사용된다. try-catch문의 끝에 선택적으로 덧붙여 사용할 수 있으며, try-catch-finally의 순서로 구성된다.

```java
try {
  // 예외가 발생할 가능성이 있는 문장들을 넣는다.
} catch (Exception1 e1) {
  // 예외처리를 위한 문장을 적는다.
} finally {
  // 예외의 발생 여부에 관계없이 항상 수행되어야 하는 문장들을 넣는다.
  // finally블럭은 try-catch문의 맨 마지막에 위치해야 한다.
}
```

예외가 발생한 경우에는 'try -> catch -> finally'의 순으로 실행되고, 예외가 발생하지 않은 경우에는 'try -> finally'의 순으로 실행된다. 아래와 같이 프로그램을 설치하는 코드가 있을 때, 설치를 정상적으로 마쳐도 임시파일을 삭제해야 하고 중간에 예외가 발생해도 임시파일을 삭제해야 한다.

```java
try {
  startInstall();     // 프로그램 설치에 필요한 준비를 한다.
  copyFiles();        // 파일들을 복사한다.
  deleteTempFiles();  // 프로그램 설치에 사용된 임시파일들을 삭제한다.
} catch (Exception e) {
  e.printStackTrace();
  deleteTempFiles();  // 프로그램 설치에 사용된 임시파일들을 삭제한다.
} // try-catch의 끝
```

이럴 때 같이 try블럭과 catch블럭에 같은 코드를 넣기보다는 아래와 같이 finally블럭에 넣는 것이 낫다.

```java
try {
  startInstall();     // 프로그램 설치에 필요한 준비를 한다.
  copyFiles();        // 파일들을 복사한다.
} catch (Exception e) {
  e.printStackTrace();
} finally {
  deleteTempFiles();  // 프로그램 설치에 사용된 임시파일들을 삭제한다.
}
```

try블럭 안에 return문이 있어서 try블럭을 벗어나갈 때에도 finally블럭이 실행된다.
