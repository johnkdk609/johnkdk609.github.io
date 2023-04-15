---
layout: post
title: 접근 제어자(access modifier)
description: Java의 정석 기초편 ch. 7 객체지향 프로그래밍 2
categories: Java
date: 2023-04-15 18:41:00 +09:00
---
접근 제어자(access modifier)는 멤버 또는 클래스에 사용되어, 해당하는 멤버 또는 클래스를 외부에서 접근하지 못하도록 제한하는 역할을 한다.

접근 제어자가 default임을 알리기 위해 실제로 default를 붙이지는 않는다. 클래스나 멤버변수, 메서드, 생성자에 접근 제어자가 지정되어 있지 않다면, 접근 제어자가 default임을 뜻한다.

> 접근 제어자가 사용될 수 있는 곳 - 클래스, 멤버변수, 메서드, 생성자
>
> **private** - 같은 클래스 내에서만 접근이 가능하다.\
> **(default)** - 같은 패키지 내에서만 접근이 가능하다.\
> **protected** - 같은 패키지 내에서, 그리고 다른 패키지의 자손 클래스에서 접근이 가능하다.\
> **public** - 접근 제한이 없다.

접근 범위가 넓은 쪽에서 좁은 쪽의 순으로 왼쪽부터 나열하면 다음과 같다.

**public > protected > (default) > private**

<table>
  <tr>
    <th>제어자</th>
    <th>같은 클래스</th>
    <th>같은 패키지</th>
    <th>자손 클래스</th>
    <th>전체</th>
  </tr>
  <tr>
    <td style="text-align:center">public</td>
    <td style="text-align:center">O</td>
    <td style="text-align:center">O</td>
    <td style="text-align:center">O</td>
    <td style="text-align:center">O</td>
  </tr>
  <tr>
    <td style="text-align:center">protected</td>
    <td style="text-align:center">O</td>
    <td style="text-align:center">O</td>
    <td style="text-align:center">O</td>
    <td style="text-align:center"></td>
  </tr>
  <tr>
    <td style="text-align:center">(default)</td>
    <td style="text-align:center">O</td>
    <td style="text-align:center">O</td>
    <td style="text-align:center"></td>
    <td style="text-align:center"></td>
  </tr>
  <tr>
    <td style="text-align:center">private</td>
    <td style="text-align:center">O</td>
    <td style="text-align:center"></td>
    <td style="text-align:center"></td>
    <td style="text-align:center"></td>
  </tr>
</table>

public은 접근 제한이 전혀 없는 것이고, private은 같은 클래스 내에서만 사용하도록 제한하는 가장 높은 제한이다. 그리고 defualt는 같은 패키지 내의 클래스에서만 접근이 가능하도록 하는 것이다.

마지막으로 protected는 패키지에 관계없이 상속관계에 있는 자손 클래스에서 접근할 수 있도록 하는 것이 제한 목적이지만, 같은 패키지 내에서도 접근이 가능하다. 그래서 protected가 default보다 접근 범위가 더 넓다. 

(접근 제어자가 default라는 것은 아무런 접근 제어자도 붙이지 않는 것을 의미한다.)
