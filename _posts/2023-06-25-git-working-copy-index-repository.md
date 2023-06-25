---
layout: post
title: working copy & index & repository
description: 지옥에서 온 Git 섹션 5. working copy & index & repository
categories: Git
date: 2023-06-25 23:33:00 +0900
---
git reset이라는 명령어를 사용할 때 옵션이 몇 가지 있다. ```git reset --help```를 입력하면, ```--soft```, ```--mixed```, ```--hard``` 등이 보인다. 이 세 가지가 주로 많이 사용된다.

깃의 굉장히 중요한 내부 구조인 'working directory', 'index' 그리고 'repository'라는 세 가지 구조에 대해 이해해볼 것이다.

<table>
  <tr>
    <th>working directory<br>working tree<br>working copy</th>
    <th>index<br>staging area<br>cache</th>
    <th>repository<br>history<br>tree</th>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td>git reset --soft</td>
  </tr>
  <tr>
    <td></td>
    <td colspan="2" text-align="right">git reset --mixed</td>
  </tr>
  <tr>
    <td colspan="3" text-align="right">git reset --hard</td>
  </tr>
</table>

우선, 세 가지 요소가 있다. 첫 번째는 working directory로 실제 작업을 진행하는 곳이다. 그리고 이것을 git add를 했을 때 포함되는 곳이 index이다. 그리고 버전들 즉 커밋들이 저장되는 곳이 respository이다. (repository라는 표현은 굉장히 광범위하게 사용된다. 그런데 이렇게 세 가지의 요소가 언급된 경우에 repository는 커밋된 버전들이 저장되는 곳을 뜻한다.)

working directory와 같은 뜻의 다른 명칭으로는 working tree, working copy가 있다. index는 staging area, cache 라고도 불린다. repository는 history, tree라고도 불린다.

git reset는 각각 옵션을 무엇을 주는가에 따라 삭제되는 대상이 달라진다. ```git reset --hard```는 세 영역 전부 초기화 된다. ```git reset --soft```의 경우 respository만 초기화 되고 나머지는 건드리지 않는다. ```git reset --mixed```는 index와 repository는 취소하지만 working directory는 건드리지 않는다.

이제 실습을 해보겠다. gitfh2 파일을 초기화하고, git init을 한다. 그리고 f1.txt라는 파일을 열어 처음에 "init"이라는 내용으로 저장하고, 커밋 메시지 1로 커밋한다. 다음으로 다시 f1.txt를 열어 "init"을 지우고 "repository"를 적고 저장한다. 그리고 커밋 메시지 2로 커밋한다.
