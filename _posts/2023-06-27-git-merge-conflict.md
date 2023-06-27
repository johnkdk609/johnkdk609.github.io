---
layout: post
title: merge & conflict
description: 지옥에서 온 Git 섹션 5. merge & conflict
categories: Git
date: 2023-06-27 14:38:00 +0900
---
Git은 branch를 효율적으로 다룰 수 있게 하는 혁신을 가져왔다. branch가 활성화됨에 따라 수많은 충돌(conflict)이 발생했다. 이번에는 충돌이 일어났을 때 깃은 내부적으로 어떤 변화를 갖게 되는지 알아볼 것이다.

우선 gitfh2 디렉토리를 git init으로 초기화한다. 그리고 f1.txt 파일을 생성해 다음과 같은 내용으로 저장 및 git add, git commit을 한다.

<img width="201" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/632666a7-e2b1-426b-916a-3f4926e422ac">

이제 f1.txt를 가지고 있는 다른 branch를 만들 것이다. ```git checkout -b exp```를 입력해 한 번에 exp branch로 넘어간다.

그리고 exp branch에서 f1.txt를 열어서 내용을 'common'을 'exp'로 수정하고 저장한다. 그리고 커밋한다.

<img width="376" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/9e348044-9fb2-4175-a2da-415128d32cf5">

다시 main branch로 체크아웃하고, f1.txt 파일을 열어서 내용을 'common'에서 'main'으로 수정하고 저장한다. 그리고 커밋한다.

<img width="372" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e27822bc-a774-45ee-bed6-1e077a633987">

이제 main branch에서 ```git merge exp```를 입력하면 다음과 같이 CONFLICT가 발생했다는 메시지가 뜬다. 

<img width="526" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4f53a749-4453-4bf4-a956-04432815c6bb">

f1.txt라는 파일 때문에 자동 merge가 실패했다고 나타난다. 

gistory를 열어서 확인하면, 우선 index 파일에 들어갔을 때 오른쪽에 숫자가 붙어있고, 그 숫자 뒤에 파일명이 붙어있다. 보통 이 숫자는 0이었는데, 충돌이 일어났을 때에는 0이 아닌 숫자가 붙는다. 여기 있는 것을 클릭해보면, 1번은 'common', 2번은 'main', 3번은 'exp'가 function의 내용으로 들어있다.

<img width="833" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1702cb01-0f90-4fd6-a138-7fd62dff702e">

이 세 가지 정보를 통해서 '3 way merge`라고 하는 병합이 이뤄진다. 이 병합이 매우 똑똑하게 이뤄지기 위해서는 공통이 되는 파일과 각자의 파일들 총 세 개가 있어서 이 세 개를 근거로 해서 자동으로 깃은 병합 작업을 시도한다.

gistory에서 MERGE_HEAD는 merge가 될 대상의 최신 커밋이다. 

현재 vim f1.txt를 입력하면 다음과 같이 나타난다.

<img width="210" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5d64f2fd-1ac4-4d41-bc57-7d881c5e0a74">

병합 작업을 할 때 병합을 전문적으로 해주는 도구인 kdiff3를 사용해볼 것이다. kdiff3를 우선 설치한다. 설치를 완료하고 ```git config --global merge.tool kdiff3```라고 입력해 세팅한다. 그리고 ```git mergetool```이라고 입력하면 다음과 같은 화면이 뜬다.

<img width="302" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/bf71eb6f-13be-4835-b0cf-a561d92bfc7f">

그리고 다음과 같이 kdiff3가 작동한다.

<img width="866" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c1b8b387-fc61-4284-8548-ffd50b88166b">

크게 네 개로 구분되어 있다. 왼쪽 A는 Base, 가운데 B는 Local(현재 체크아웃 되어 있는 branch), 오른쪽 C는 Remote, 그리고 아래는 Output이다. 충돌이 난다는 것은 main의 수정사항과 exp의 수정사항이 있고, 이 둘 간의 공통적인 내용인 common이 있다는 것이다. 이런 공통적인 내용을 Base라고 부른다.

<img width="308" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b2eec060-07d8-4767-8429-b14a59a36c0c">

kdiff3에서 아래에 빨간색으로 \<Merge Conflict\>라고 되어 있는 부분을 클릭하고, 창 오른쪽 위에 A, B, C를 클릭하면 각각의 상황이 적용된다. 일단은 Base인 A로 바꾸고, 내용을 다음과 같이 수정한다. 

<img width="240" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/dc0ca17e-9c15-4a33-b4bc-8ffda58c69d2">

이제 저장 및 창을 끄면, 터미널에서는 자동으로 ```git mergetool```이라는 명령은 종료되면서 merge가 완료된다. 

이제 ```cat f1.txt```를 입력하면 다음과 같이 변경되어 있다. 

<img width="232" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b1b77d12-8c39-4c87-ba49-ec721ba61290">

그리고 gistory에서 index 파일을 열면 다음과 같이 방금 merge한 main과 exp를 갖게 되는 상태가 된다. 

<img width="831" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/8afaadfc-42cf-4d64-8ade-33e30ba53391">

```git status```를 입력하면 f1.txt가 add된 상태인 것을 볼 수 있다.

<img width="530" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/4658c94b-8061-42b5-887d-9667c413f84c">

f1.txt.orig는 만약의 사태를 대비해서 만들어진 파일이다. ```cat f1.txt.orig```를 입력하면 다음과 같이 나오는데, 그냥 지워도 된다. 지울 때에는 ```rm f1.txt.orig```을 입력한다. 

<img width="190" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/351efe70-beb9-48d7-b8ef-7556c4ff2dfe">

이제 ```git commit```을 입력한 다음 ```:wq```로 저장한다. 이제 ```git log```을 입력하면 다음과 같이 병합이 끝난 것을 볼 수 있다. 

<img width="503" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/bbd24567-06e2-4079-b504-756052285167">
