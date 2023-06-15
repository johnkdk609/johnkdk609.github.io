---
layout: post
title: git stage area
description: 지옥에서 온 Git 섹션 1. git stage area
categories: Git
date: 2023-06-15 15:01:00 +0900
---
우선, 기존에 만들었던 f1.txt 파일과 똑같은 파일을 만들겠다.

gitfh2 디렉토리에 가서, 터미널에서 ```cp f1.txt f2.txt```를 입력한다. (f2.txt라는 파일에 f1.txt를 카피한다는 뜻이다.)

그리고 ```git status```를 입력하면, 아래와 같이 f2.txt 파일은 untracked 즉 버전 관리가 되고 있지 않다고 나온다.

<img width="619" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2257163d-6b4f-49c0-9127-a018a938c05c">

이제 ```git add f2.txt```를 입력하여 버전 관리를 시작한다. 그리고 ```git commit```을 입력하고 commit message를 "3"으로 입력하고 저장 및 vim 빠져나가기를 한다.

그 다음, ```git log```을 입력해 로그 즉 역사를 확인한다.

<img width="525" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/731d562d-480b-4a55-830a-78b67d7ce1ec">

이렇게 방금 생성한 버전 3번이 나오고, 저자 및 버전이 만들어진 시기가 나온다.

이번에는 f1.txt와 f2.txt를 모두 수정할 것이다.

우선 ```vim f1.txt```를 누르고, "f1.txt : 4"로 내용을 변경, 저장 및 빠져나가기를 한다. ```vim f2.txt```로 들어가서 "f2.txt : 2"로 변경, 저장 및 빠져나가기를 한다.

그 다음에 ```git status```를 입력하면 다음과 같이 출력된다.

<img width="583" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f5b7f5d2-c4fe-4bcb-8ef1-580d8ca1e354">

파일 두 개가 modified된 상황이라는 것이다.

여기서 한 가지 의문점이 생긴다. 왜 commit하기 전에 꼭 add를 해야 하는 것일까?

프로젝트를 하다 보면 여러 소스 코드를 수정하게 된다. 그런데 소스 코드를 수정하다 보면 커밋하는 시기를 놓칠 때가 자주 있다. 커밋 하나는 하나의 작업을 담고 있는 것이 가장 이상적이다. 그런데 여러 개의 작업들을 담은 거대한 버전 하나를 만들어야 하는 상황이 올 수 있다. 이때 Git은 add라는 과정을 통해서 내가 커밋하고자 하는 파일만 커밋하게 할 수 있다.

가령 터미널에 ```git add f1.txt```를 입력하고, ```git status```를 입력하면 다음과 같이 출력된다.

<img width="589" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4af725f9-77fc-4990-8095-24bf21d62add">

똑같은 modified인데 f1.txt는 초록색이고, 'Changes to be committed'라고 되어 있다. 반면에 f2.txt는 'Changes not staged for commit'라고 되어 있다.

이 상태에서 ```git commit```를 입력하면 f1.txt는 새로운 버전에 포함되지만, f2.txt는 포함되지 않는다. **즉, 선택적으로 파일을 커밋할 수 있게 된다는 것**이다.

이제 ```git commit```를 입력하고, commit message를 "4"로 변경한 다음 저장 및 vim 빠져나가기를 한다. 그리고 ```git log```를 입력하면 다음과 같이 출력된다.

<img width="531" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/42755e60-d847-477e-9d46-1fb694b65d0a">

그 다음에 ```git status```를 입력하면, 아래와 같이 add를 하지 않은 f2.txt는 modified 상태로 남아있다.

<img width="583" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/228d93da-f3c7-41f2-91cf-db7200a985e7">

Git은 add라는 절차를 두었기 때문에 commit을 할 것들만 추려서 선택적으로 커밋을 할 수 있게 된 것이다.

```git add f1.txt```라는 명령은 f1.txt를 커밋 대기 상태에 들어가게 한다. 이때 ```git commit```이라는 명령을 실행하면, 깃은 커밋 대기 상태에 있는 파일들만을 그 버전에 포함시킨다.

이 커밋 대기 상태를 '***stage area***'라고 한다. 즉, 커밋 대기할 파일들의 stage인 것이다.

stage는 커밋 대기 상태에 있는 파일들이 있는 곳이고, 커밋이 된 결과가 저장되는 곳이 repository이다.
