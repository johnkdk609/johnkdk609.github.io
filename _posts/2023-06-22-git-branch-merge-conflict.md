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

즉, 파일이 다르면 무조건 자동으로 합쳐진다. 그런데 파일이 같으면 문제가 생기기 시작한다.

main branch와 exp branch에 같은 이름의 파일을 만들 것이다. 우선 exp branch로 체크아웃 하고 common.txt라는 파일을 만들고, main branch에서도 common.txt라는 파일을 만들 것이다. exp branch에서 common.txt를 만들고, 내용은 'function a() {}'로 해서 저장한다. git add 및 git commit을 커밋 메시지 8로 한다. 그리고 main branch로 체크아웃 해서 ```git merge exp```를 한다. 그러면 main branch에도 common.txt 파일이 생성될 것이다.

이 상태에서 main branch의 common.txt를 열어서 기존 내용 위에 'function b() {}'를 추가하고 저장한다. 그리고 커밋 메시지 9로 커밋한다.

다음으로 exp branch로 체크아웃 하고, common.txt 파일을 열어 기존 내용 아래에 'function c() {}'를 내용으로 추가 및 커밋 메시지 10으로 커밋한다.

common.txt라는 파일을 main branch와 exp branch에서 각각 다른 부분을 수정했다.

main branch에서 ```git merge exp```를 입력하면 병합이 잘 된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/a0294741-74c8-4802-a2e8-302df2a6ec9d)

```cat common.txt```로 병합된 common.txt의 내용을 읽어보면 다음과 같이 변경되었다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0e3e678f-918f-42d1-8347-49cb6196c25b)

같은 파일임에도 서로 수정한 위치가 다르다면 자동으로 합치는 것이다. 버전 관리를 사용하는 굉장히 중요한 이유 중 하나이다.

하지만, 같은 부분을 수정할 때 복잡해진다.

우선 exp branch로 체크아웃하고 ```git merge main```을 입력해 exp branch와 main branch의 파일 내용을 같게 한다.

그리고 다시 main branch로 체크아웃 하고 common.txt의 내용 중 'function a(main) {}'로 수정 및 커밋 메시지 '11'로 커밋한다.

그 다음 exp branch로 체크아웃 하고, common.txt의 내용 중 'function a(exp) {}'로 수정 및 커밋 메시지 '12'로 커밋한다.

이제 main branch로 체크아웃 하고, ```git merge exp```를 입력한다. 그러면 CONFLICT가 발생한다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0e83b7ea-068f-401a-b063-0fe58068bcac)

```git status```를 입력하면 다음과 같이 'You have unmerged paths.'라고 뜬다. common.txt가 양쪽 모두에서 수정이 되었기 때문에 병합하는 데에 실패했다는 것이다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/8a0a5121-6a0e-4620-af20-fb2ec9087a9d)

```vim common.txt```를 입력하고 common.txt 파일을 연다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/3f288f6a-587b-44e0-aa5e-7933bbf4261f)

그러면 위와 같은 화면이 나타난다. 중간에 있는 '=======' 구분자를 중심으로 위에 '<<<<<<< HEAD'라고 되어 있는 부분이 현재 내가 체크아웃 되어 있는 main branch의 수정 사항이다. 그리고 '>>>>>>> exp'라고 되어 있는 부분이 exp branch의 내용이다. 깃이 자기가 자동으로 병합하는 것이 실패했기 때문에, merge를 실행한 내가 이 문제를 해결하라고 위임하는 것이다. 그리고 충돌이 발생한 부분을 표시해주는 것이다.

여기서 'function a(main, exp) {}'로 내용을 수정하고 구분자들을 삭제한 다음 저장 및 빠져나가기를 한다.

그리고 ```git add common.txt```를 입력한 다음 ```git status```를 입력하면 다음과 같이 나타난다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/baf58453-71a8-4bf1-b063-5715cd2737fb)

이제 ```git commit```를 입력한 다음, 저장 및 빠져나가기를 한다.

```cat common.txt```를 입력하면 다음과 같이 잘 수정되어 있는 것을 알 수 있다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2b17c2db-952b-4ab8-85ef-48c6a01c9222)
