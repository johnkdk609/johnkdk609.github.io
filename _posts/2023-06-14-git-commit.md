---
layout: post
title: 버전 만들기 (commit)
description: 지옥에서 온 Git 섹션 1. 버전 만들기 (commit)
categories: Git
date: 2023-06-14 13:43:00 +0900
---
지금까지 작업한 내용을 새로운 버전으로 만들어서 저장하려고 한다. 이렇게 저장이 되면 예전으로 돌아갈 수도 있고, 파일의 변경사항의 이력을 볼 수도 있다.

버전이 무엇인지 생각해볼 필요가 있다. 버전과 변화의 차이는, 버전은 의미 있는 변화라는 것이다. 어떤 작업이 있으면, 그 작업이 완결된 상태가 '버전(version)'이다.

일단 해당 디렉토리인 gitfh2에 가서 ```git status```라고 터미널에 입력하면 다음과 같이 출력된다.

<img width="386" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/13ddac6e-0b0f-42b6-a6c5-ed3bdaa9dd44">

f1.txt는 ```git add f1.txt```를 통해 tracking 되고 있는 것이다.

버전을 만들기 전에 우리가 해야 할 일이 있다. 지금부터 작성할 버전들이 '내가 만든 것이다'라는 것을 다른 사람들에게 알려주기 위해서 이름을 세팅해야 한다.

```git config --global user.name 이름(별명)```

```git config --global user.email 이메일```

그러면 내가 작성한 버전은 이름(별명)과 이메일 주소를 포함하고 있는 버전이 된다. 다른 사람이 봤을 때 누가 작성한 것인지 알 수 있게 되는 것이다. 이 작업은 딱 한 번만 수행하면 된다.

이제 ```git commit```을 터미널에 입력하면 vim이 실행된다. 그리고 그 밑에는 지금 어떤 상태인지 보여진다.

<img width="642" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e693391d-d84a-40ee-bb63-638671e1cdeb">

여기서 맨 윗줄에 현재 버전의 메세지를 적는다. 버전의 메세지라는 것은 이 변화가 어떤 변화를 담고 있는지, 이 파일들이 왜 변경되었는지 그 이유를 적은 것이다. 이것을 'commit message'라고 부른다.

vim이니 입력하기 위해 ```i```를 입력하고, 맨 윗줄에 1을 입력한다. 그리고 esc를 누르고, ```:wq```를 기입해 저장 및 vim 빠져나가기를 한다.

<img width="259" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/35a0f5ab-3e04-4e7e-8308-f121740b3a57">

그러면 이러한 메세지가 뜬다. 'f1.txt라는 파일이 새로운 버전이 되었다'는 메세지이다.

현재 버전이 잘 만들어졌는지 확인하기 위해서 ```git log```를 터미널에 입력한다.

<img width="510" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5bd83485-22f5-4ae0-84ed-e6cb399c01a8">

이렇게 숫자 1을 버전 메세지로 하는 버전이 생성된 것을 볼 수 있다. 이 버전을 작성한 사람은 'kdk'이고, 이 사람의 이메일도 볼 수 있다.

이제 터미널에서 ```ls -al```이라고 입력하면, f1.txt 파일이 있는 것을 알 수 있다.

<img width="404" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7b29f543-2b17-4903-92bb-2333d5569701">

이제 실습 차원에서 몇 번 더 버전을 만들어보겠다. f1.txt라는 파일을 다시 버전으로 만들려면, ```vim f1.txt```를 입력해 내용을 수정한다. ```i```를 입력하고, 'source : 2'로 변경한 다음, esc를 누르고 ```:wq```를 입력하여 저장 및 빠져나가기를 한다.

이제 ```git status```로 현재 상태를 파악하면, 

<img width="585" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0a1dbb5c-4b77-418d-b307-b59608aef116">

위와 같이 나온다. 즉, f1.txt라는 파일이 이제는 빨간색으로 표시가 되었는데, 'modified' 즉 수정되었다고 나온다.

우선 ```git add f1.txt```를 하여 버전 관리 시스템에 add를 해주어야 한다. 깃에서 어떤 파일이 생겼을 때, 그 파일에 대한 버전 관리를 깃에게 명령할 때에도 우리는 add를 쓴다. 그리고 버전 관리가 이미 되고 있는 파일(이 경우 f1.txt)이 수정돼서 우리가 버전을 생성할 때에도 add를 또 해주어야 한다. **즉, 최초로 추적할 때도 add를 하고, 파일이 수정되어서 버전을 만들기 전에도 add를 먼저 해야 한다.**

<img width="433" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/d8739993-9549-402d-981e-09aa597d45c6">

그리고 ```git commit```를 하고, commit message를 '2'로 수정한다. 

<img width="378" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5709feaa-516a-4fe2-b479-8e7c554cbd4e">

그런 다음 ```git log```를 입력하면 다음과 같이 방금 커밋한 숫자 2가 나온다.

<img width="514" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/b800941a-3745-403a-95bd-3e2576334ae6">
