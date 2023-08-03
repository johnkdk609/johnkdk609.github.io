---
layout: post
title: 입출력(I/O)과 스트림(stream)
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-07-31 23:22:00 +0900
---
I/O란 Input과 Output의 약자로 입력과 출력, 간단히 줄여서 입출력이라고 한다. 입출력은 컴퓨터 내부 또는 외부의 장치와 프로그램 간의 데이터를 주고받는 것을 말한다.

예를 들면 키보드로부터 데이터를 입력 받는다든가 System.out.println()을 이용해서 화면에 데이터를 출력한다든가 하는 것이 가장 기본적인 입출력의 예이다.


## 스트림(stream)

자바에서 입출력을 수행하려면, 즉 어느 한 쪽에서 다른 쪽으로 데이터를 전달하려면, 두 대상을 연결하고 데이터를 전송할 수 있는 무언가가 필요한데 이것을 스트림(stream)이라고 정의했다. 입출력에서의 스트림은 14장의 스트림과 같은 용어를 쓰지만 다른 개념이다.

(스트림은 TV와 DVD를 연결하는 입력선과 출력선과 같은 역할을 한다.)

> 스트림이란 데이터를 운반하는 데 사용되는 연결 통로이다.

스트림은 연속적인 데이터의 흐름을 물에 비유해서 붙여진 이름인데, 여러 가지로 유사한 점이 많다. 물이 한쪽 방향으로만 흐르는 것과 같이 스트림은 단방향통신만 가능하기 때문에 하나의 스트림으로 입력과 출력을 동시에 처리할 수 없다.

그래서 입력과 출력을 동시에 수행하려면 입력을 위한 입력스트림(input stream)과 출력을 위한 출력스트림(output stream), 모두 2개의 스트림이 필요하다.

![inputStreamOutputStream](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ffe58581-0f91-419f-9aa7-9211cbc15db5)

스트림은 먼저 보낸 데이터를 먼저 받게 되어 있으며 중간에 건너뜀 없이 연속적으로 데이터를 주고받는다. 큐(queue)와 같은 FIFO(First In First Out) 구조로 되어 있다고 생각하면 이해하기 쉬울 것이다.