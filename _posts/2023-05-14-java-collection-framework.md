---
layout: post
title: 컬렉션 프레임워크(Collection Framework)
description: Java의 정석 기초편 ch. 11 컬렉션 프레임웍
categories: Java
date: 2023-05-14 22:53:00 +09:00
---
컬렉션 프레임워크(Collection Framework)란, '**데이터 군을 저장하는 클래스들을 표준화한 설계**'를 뜻한다. 컬렉션(collection)은 다수의 데이터 즉 데이터 그룹을, 프레임워크(framework)은 표준화된 프로그래밍 방식을 의미한다.

(Java API문서에서는 컬렉션 프레임워크를 '데이터 군(group)을 다루고 표현하기 위한 단일화된 구조(architecture)'라고 정의하고 있다.)

컬렉션 프레임워크는 컬렉션(다수의 데이터)을 다루는 데에 필요한 다양하고 풍부한 클래스들을 제공하기 때문에 프로그래머의 짐을 상당히 덜어주고 있으며, 또한 인터페이스와 다형성을 이용한 객체지향적 설계를 통해 표준화되어 있기 때문에 사용법을 익히기에도 편리하고 재사용성이 높은 코드를 작성할 수 있다는 점에서 장점이 있다. 


## 라이브러리와 프레임워크

라이브러리(그래픽 라이브러리, 통계 라이브러리 등)는 공통으로 사용될 만한 유용한 기능을 모듈화하여 제공하는데 비해, 프레임웍은 단순히 기능뿐만 아니라 프로그래밍 방식을 정형화하여 프로그램의 개발 생산성을 높이고 유지보수를 용이하게 한다.

JDK1.8에 이르러서야 비로소 '람다와 스트림'에 의해 컬렉션 프레임워크가 이루지 못한 표준화, 즉 다양한 종류의 데이터를 동일한 방식으로 다루는 것이 가능해졌다.


## 컬렉션 프레임워크의 핵심 인터페이스

컬렉션 프레임워크에서는 컬렉션 데이터 그룹을 크게 3가지 타입이 존재한다고 인식하고 각 컬렉션을 다루는 데에 필요한 기능을 가진 3개의 인터페이스를 정의하였다. 그리고 인터페이스 List와 Set의 공통된 부분을 다시 뽑아서 새로운 인터페이스인 Collection을 추가로 정의하였다. 

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/62e37960-7058-4928-892a-6664f91544de" width="400px" title="컬렉션 프레임워크의 핵심 인터페이스 간의 상속계층도" alt="컬렉션 프레임워크의 핵심 인터페이스 간의 상속계층도"  align="center">

인터페이스 List와 Set을 구현한 컬렉션 클래스들은 서로 많은 공통점이 있어서, 공통된 부분을 다시 뽑아 Collection인터페이스를 정의할 수 있었지만 Map인터페이스는 이들과는 전혀 다른 형태로 컬렉션을 다루기 때문에 같은 상속계층도에 포함되지 못했다.

<table>
  <tr>
    <th>인터페이스</th>
    <th>특징</th>
  </tr>
  <tr>
    <td rowspan="2" style="text-align:center">List</td>
    <td>순서가 있는 데이터의 집합. 데이터의 중복을 허용한다.<br>예) 대기자 명단</td>
  </tr>
  <tr>
    <td>구현클래스: ArrayList, LinkedList, Stack, Vector 등</td>
  </tr>
  <tr>
    <td rowspan="2" style="text-align:center">Set</td>
    <td>순서를 유지하지 않는 데이터의 집합. 데이터의 중복을 허용하지 않는다.<br>예) 양의 정수집합, 소수의 집합</td>
  </tr>
  <tr>
    <td>구현클래스: HashSet, TreeSet 등</td>
  </tr>
  <tr>
    <td rowspan="2" style="text-align:center">Map</td>
    <td>키(key)와 값(value)의 쌍(pair)으로 이루어진 데이터의 집합<br>순서는 유지되지 않으며, 키는 중복이 허용되지 않고, 값은 중복을 허용한다.<br>예) 우편번호, 지역변호(전화번호)</td>
  </tr>
  <tr>
    <td>구현클래스: HashMap, TreeMap, HashTable, Properties 등</td>
  </tr>
</table>

(키(Key)란, 데이터 집합 중에서 어떤 값(value)을 찾는데 열쇠(key)가 된다는 의미에서 붙여진 이름이다. 그래서 키(Key)는 중복이 되지 않는다.)

컬렉션 프레임워크의 모든 컬렉션 클래스들은 List, Set, Map 중의 하나를 구현하고 있으며, 구현한 인터페이스의 이름이 클래스의 이름에 포함되어 있어서 이름만으로도 클래스의 특징을 쉽게 알 수 있도록 되어있다.
