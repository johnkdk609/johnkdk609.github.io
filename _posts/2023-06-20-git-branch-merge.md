---
layout: post
title: branch 병합
description: 지옥에서 온 Git 섹션 3. branch 병합
categories: Git
date: 2023-06-20 14:17:00 +0900
---
이번에는 작업들을 병합(merge)하는 것에 대해 알아볼 것이다.

gitfh2 디렉토리에서, ```git log --branches --decorate --graph --oneline```을 입력한다.

<img width="221" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/04e9fb4e-355d-47cc-b6ec-e00ddea03211">

1, 2는 공통이고, 2에서 exp는 3, 4로 갔고, main은 5로 왔음을 알 수 있다.

지금까지 exp에서 작업했던 내용을 main으로 병합하는 방법을 살펴볼 것이다. exp의 내용을 main으로 옮기는 것이지, main의 내용을 exp로 옮기는 것이 아니다.

main branch가 exp에서 작업했던 3, 4라는 두 개의 커밋 내용을 갖게 하는 것이 첫 번째 목표이다.

exp를 main으로 가져오려면, 우선 main branch로 체크아웃 하고 그 상태에서 ```git merge exp```를 해야 한다. 그러면 에디터가 열리는데, ```:wq```로 저장 및 빠져나가기를 한다.

<img width="283" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/028d2c4a-7002-412a-945c-7e1ad9392696">

다시 ```git log --branches --decorate --graph --oneline```을 입력하면 다음과 같이 출력된다.

<img width="375" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e1675c2f-7d49-4a4e-b90d-4aa3ed605394">

현재 main은 '26111af' 커밋으로 체크아웃 되어 있다. 그리고 이것의 커밋 메세지는 자동으로 작성된 Merge branch 'exp' 이다. 이 커밋은 두 개의 부모 커밋을 가진다. 첫 번째는 원래 main이 갖고 있었던 커밋인 5번을 부모로 가지고 있다. 동시에 exp가 작업했던 3과 4를 부모로 가져간다. 이제 main은 자기가 작업했던 5번과, exp가 작업했던 3, 4를 모두 가지고 있는 커밋이 된 것이다.

main branch에서 ```ls -al```을 입력해보면, main은 드디어 f1.txt, f2.txt, f3.txt를 모두 가지는 상태가 된 것임을 알 수 있다.

<img width="406" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/ac62b62f-6e5b-4dd2-a4ff-993b8a05ee62">

exp는 3, 4를 가지고 있지만 5를 가지고 있지 못하다. exp가 main이 작업했던 5를 포함하도록 할 것이다. 그래서 exp와 main이 완전히 같은 상태가 되도록 해볼 것이다.

```git checkout exp```를 하고, ```git merge main```을 입력한다. 그러면 main을 exp로 가져오는 것이다.

<img width="263" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/6456f02c-24c7-480b-ac7b-d81200bedbf0">

이제 ```git log --branches --decorate --graph --oneline```를 입력해보면 다음과 같이 출력된다.

<img width="409" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1a54dcef-2123-4b22-afd6-ad02bb150f95">

이제 exp가 main과 똑같은 커밋을 최신 커밋으로 가지고 있고, exp는 main과 똑같이 3, 4, 5를 공통의 부모로 갖고 있다. 이제 exp와 main은 완전히 똑같은 상태가 된 것이다.

이제 작업이 끝났으니 더 이상 exp가 필요하지 않다. 작업이 끝났으니 둬도 되지만, 지워도 된다.

```git checkout main```을 입력해 main으로 돌아가고, ```git branch -d exp```를 입력한다. 그러면 exp branch가 삭제된다.

<img width="275" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/792db846-4e29-4db1-94f5-a9379287577f">

그리고 ```git log --branches --decorate --graph --oneline```을 입력하면,

<img width="376" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/66171a9b-f25e-4459-bfb4-66bf3a2fb1e5">

이렇게 깔끔하게 main만 남고 exp는 사라진 것을 알 수 있다.
