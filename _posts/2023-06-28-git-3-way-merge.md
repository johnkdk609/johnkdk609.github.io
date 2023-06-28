---
layout: post
title: 3 way merge
description: 지옥에서 온 Git 섹션 5. 3 way merge
categories: Git
date: 2023-06-28 14:56:00 +0900
---
merging을 할 때 사용하는 기법인 '3 way merge'에 대해 알아볼 것이다. 3 way merge와 2 way merge의 차이점을 알아보고 왜 3 way merge가 훌륭한지 알아볼 것이다.

우선 아래와 같이 4행으로 이루어진 코드(Base)가 있었다고 가정하겠다. 여기서 내 branch(Me)와 상대방의 branch(Other)가 있다. 

<table>
  <tr>
    <th>Me</th>
    <th>Base</th>
    <th>Other</th>
  </tr>
  <tr align="center">
    <td>A</td>
    <td>A</td>
    <td></td>
  </tr>
  <tr align="center">
    <td>B</td>
    <td>B</td>
    <td>B</td>
  </tr>
  <tr align="center">
    <td>1</td>
    <td>C</td>
    <td>2</td>
  </tr>
  <tr align="center">
    <td></td>
    <td>D</td>
    <td>D</td>
  </tr>
</table>

나는 A라는 부분을 그냥 두었고, 다른 사람은 똑같은 파일에 대해 A를 지웠다. 나는 B라는 부분을 그냥 두었고, 상대방도 똑같이 B를 그냥 두었다. 나는 C라는 부분을 1로 수정했고, 상대방은 C를 2로 수정했다. 그리고 나는 D라는 부분을 지웠고 상대방은 D를 그대로 두었다. 

나(Me)와 다른 사람(Other)이 공통으로 가지고 있는 기존의 내용이 Base이다. 


## 2 way merge

merge 방법은 크게 두 가지 방법이 있는데, 2 way merge가 있고, 3 way merge가 있다. 2 way merge는 Base를 보지 않고 두 브랜치 간의 차이점만 보고 병합을하는 방식이다. 

2 way merge는 Base를 보지 않기 때문에, 아래 표에서 Base 부분이 보이지 않게 했다. 나와 다른 사람이 코드가 같다면(B) 같은 부분이니 그냥 내버려 두면 된다. 1과 2처럼 서로 다른 부분의 경우 병합을 자동으로 하는 입장에서는 뭐가 맞는지 알 수 없다. 그래서 그냥 Conflict를 낸다. D도 마찬가지이다. 한 명은 내용이 없고, 다른 사람은 D라는 내용이 있으므로 병합을 자동으로 하는 입장에서는 무엇이 맞는지 모르니 Conflict를 낸다. 마찬가지로 A에 대해서도 한 명은 A라 하고 있고 다른 사람은 아무 내용이 없기 때문에 conflict가 발생한다.  

<table>
  <tr>
    <th>Me</th>
    <th>Base</th>
    <th>Other</th>
    <th>2 way merge</th>
  </tr>
  <tr align="center">
    <td>A</td>
    <td></td>
    <td></td>
    <td bgcolor="red">?</td>
  </tr>
  <tr align="center">
    <td>B</td>
    <td></td>
    <td>B</td>
    <td>B</td>
  </tr>
  <tr align="center">
    <td>1</td>
    <td></td>
    <td>2</td>
    <td bgcolor="red">?</td>
  </tr>
  <tr align="center">
    <td></td>
    <td></td>
    <td>D</td>
    <td bgcolor="red">?</td>
  </tr>
</table>


## 3 way merge

3 way merge는 Base를 참고로 해서 두 개의 대상을 병합하는 방식이다. 3 way merge가 훨씬 좋다. 

A의 경우 Base와 비교했을 때 나는 수정하지 않았고, 다른 사람은 수정했기 때문에 다른 사람이 수정한 것이 채택되어야 한다. 같은 이유로 D도 한 명은 수정하고 다른 사람은 수정하지 않았으므로 수정한 것이 채택되는 것이 맞다. B의 경우 나와 상대방의 코드가 같으므로 그대로 B로 간다. C의 경우 나와 상대방이 수정한 내용이 다르므로 Conflict가 발생한다. 

<table>
  <tr>
    <th>Me</th>
    <th>Base</th>
    <th>Other</th>
    <th>3 way merge</th>
  </tr>
  <tr align="center">
    <td>A</td>
    <td>A</td>
    <td></td>
    <td></td>
  </tr>
  <tr align="center">
    <td>B</td>
    <td>B</td>
    <td>B</td>
    <td>B</td>
  </tr>
  <tr align="center">
    <td>1</td>
    <td>C</td>
    <td>2</td>
    <td bgcolor="red">?</td>
  </tr>
  <tr align="center">
    <td></td>
    <td>D</td>
    <td>D</td>
    <td></td>
  </tr>
</table>

이것이 바로 3 way merge가 동작하는 방식이다.

결론적으로 2 way merge와 3 way merge를 비교한 아래의 표를 보면, 3 way merge는 merge하는 과정에서 Base를 참고함으로써 Conflict의 발생 횟수를 줄일 수 있는 것이다. 즉, 더 스마트하게 merge를 할 수 있는 것이다.

<table>
  <tr>
    <th>Me</th>
    <th>Base</th>
    <th>Other</th>
    <th>2 way merge</th>
    <th>3 way merge</th>
  </tr>
  <tr align="center">
    <td>A</td>
    <td>A</td>
    <td></td>
    <td bgcolor="red">?</td>
    <td></td>
  </tr>
  <tr align="center">
    <td>B</td>
    <td>B</td>
    <td>B</td>
    <td>B</td>
    <td>B</td>
  </tr>
  <tr align="center">
    <td>1</td>
    <td>C</td>
    <td>2</td>
    <td bgcolor="red">?</td>
    <td bgcolor="red">?</td>
  </tr>
  <tr align="center">
    <td></td>
    <td>D</td>
    <td>D</td>
    <td bgcolor="red">?</td>
    <td></td>
  </tr>
</table>



