---
layout: post
title: branch 충돌 해결
description: 지옥에서 온 Git 섹션 5. branch 충돌해결
categories: Git
date: 2023-06-22 17:23:00 +0900
---
merge를 하는 과정에서 파일의 내용이 서로 다를 경우에 충돌(conflict)이 발생할 수 있다. 이 경우에 어떻게 해야 하는지 알아볼 것이다.

우선 gitfh2 디렉토리에서 ```git branch exp```를 입력하여 exp branch를 생성한다. 만약 exp branch가 이미 존재한다고 하면, ```git branch -d exp```로 exp branch를 지우고 다시 생성한다. 만약 에러가 발생하면, ```git branch -D exp```로 강제로 지운다.

그리고 main branch에서 ```vim master.txt```를 입력해 파일을 생성하고 내용을 "a"로 저장 및 빠져나가기를 한다. ```git add master.txt``` 및 ```git commit -m "6"```으로 커밋한다.

이제 ```git checkout exp```를 입력하고, ```vim exp.txt```로 파일을 생성하고 내용을 "a"로 저장 및 빠져나가기를 한다. 그리고 git add 및 커밋 메세지 "7"로 커밋도 완료한다.

이제 ```git checkout main```을 한 다음, ```git log --branches --graph --decorate```를 입력하면 다음과 같이 출력된다.

<img width="556" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/fd8e1f15-20e0-44d7-a33c-b8a9b40f23b6">

main은 6번, exp는 7번을 가리키고 있는 상태이다.

여기서 ```git merge exp```를 하게 되면, 두 개가 수정한 부분이 각자 있었기 때문에 merge commit을 생성한다.

<img width="604" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b11e5c91-409d-4d28-ae45-e5b81ab61f9f">

여기서 ```:wq```로 저장 및 빠져나가기를 한다.

다시 ```git log --branches --graph --decorate```를 입력하면 다음과 같이 출력된다.

<img width="555" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/88335b8f-41c4-48cd-b6c0-b8485ca46e42">

이제 main은 exp의 커밋과 main이 원래 가지고 있던 커밋이 병합된 새로운 커밋에 체크아웃 되어 있는 상태이다.

```ls -al```을 입력하면 다음과 같다.

<img width="439" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/8100b78c-2e2d-40f0-8a9c-4d6809f3c6d6">

main branch에 exp.txt도 있는 것을 볼 수 있다.
