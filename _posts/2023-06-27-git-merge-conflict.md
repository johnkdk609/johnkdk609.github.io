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













