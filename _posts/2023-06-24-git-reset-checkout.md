---
layout: post
title: reset checkout
description: 지옥에서 온 Git 섹션 5. reset checkout
categories: Git
date: 2023-06-24 23:06:00 +0900
---
reset이라는 기능과 checkout을 실행했을 때 어떻게 과거로 돌아가는 지 원리를 이해해볼 것이다.

우선 gitfh2 디렉토리를 삭제하고 다시 만들어서 init 한다. 그리고 main branch에서 f1.txt 파일을 생성하고, 내용을 "a", "b", "c", "d" 순으로 추가하면서 각각 커밋 메시지 1, 2, 3, 4로 커밋한다.

이제 git log를 보면 다음과 같이 나타난다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/a94de93e-cef2-4090-b064-d86800c68d84)

이때 커밋 메시지 3인 커밋으로 돌아가고 싶으면, ```git reset --hard 11946117fb61464ec7992fcd9fbf101659178277```를 입력하면 된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/332b6a9b-9519-4c24-9918-3783b4b9e0eb)

이제 git log를 보면 다음과 같이 현재 내 branch의 최신 커밋이 3번으로 돌아가 있는 것을 볼 수 있다. 그리고 이전에 있었던 커밋은 삭제된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/a826be19-f4c4-43fb-a96d-2228239508b8)

gistory를 동작시키고 보면, refs/heads/main라는 파일이 수정돼 있다. 이 파일은 현재 내가 체크아웃 해 있는 branch인 main이 어떤 커밋을 최신 커밋으로 갖고 있는가 가리키는 파일이다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2815c4e2-9301-45c0-8b98-bfc3ecf92c1c)

**reset을 한다는 것은 현재 내가 체크아웃하고 있는 branch가 가리키는 최신 커밋을 바꾸는 행위이다.**

그러면 내가 취소한 4번이라는 로그 메시지를 갖고 있던 커밋은 지워진 것일까? 깃은 웬만해서는 어떤 정보도 지우지 않는다. 나중에 파일이 정말 많아졌을 때 가비지 컬렉션이라는 것이 돌아가면서 파일을 정리할 수는 있지만, 그런 경우는 거의 일어나지 않는다. 단지 사람의 눈에 보이지 않을 뿐, 완전히 지워지는 경우는 많지 않다는 것이다.

reset을 취소하고 싶을 때 사용할 수 있는 것이 ORIG_HEAD 파일 혹은 logs/refs/heads/main 파일이다. ORIG_HEAD 파일을 열어보면 다음과 같이 내가 삭제한 4번을 가리키고 있다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/233ec632-e556-4498-9a54-e50ded506335)

그리고 logs/refs/heads/main을 보면 다음과 같이 398f76dc836f0fba64d6d8459e0b5a8483544b69 이 11946117fb61464ec7992fcd9fbf101659178277 가 되었다고 적혀 있다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/193117ae-9f86-493d-928f-cfc54b8a0041)

내가 삭제한 4번이 최신 커밋으로 바꿔치기한 3번으로 바뀌었다는 것이다.

logs/refs/heads/main이라는 파일은 main branch에서 일어나는 여러 가지 중요한 사건들을 기록해놓은 로그이다. ORIG_HEAD는 어떤 위험한 명령(정보를 잃어버릴 수 있는 것 등)을 하기 전에 깃이 현재 branch의 HEAD가 가리키고 있는 현재 커밋을 기록해두는 곳이다. 나는 이 ORIG_HEAD를 통해서 지금 실행한 위험한 명령을 취소할 수 있다.

터미널에 ```git reset --hard ORIG_HEAD```라고 입력하면 다음과 같이 4로 돌아간다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/fe922174-a1d4-485e-b3f3-a7fbe1fc3882)

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/24fc7373-c9f4-4b83-8fd1-3a604ec07b28)

이렇게 git log를 보면 reset을 취소할 수 있는 것이다.

그런데 파일을 직접 열어보는 것은 힘든 일이기 때문에, ```git reflog```를 입력해서 볼 수 있다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/65abb634-0ff6-42fe-8435-11f4f0fac7a0)

이렇게 방금 전에 했었던 일, 그 전에 했었던 일들 각각의 커밋들을 볼 수 있는 것이다. 이런 정보들을 통해서 위험한 명령을 수행했을 때 예전으로 돌아갈 수 있는 것이다.

체크아웃을 할 때 커밋 아이디를 직접 입력하고 체크아웃 할 수 있다. 가령, 커밋 3의 커밋 아이디로 체크아웃 하고 싶다면 ```git checkout 11946117fb61464ec7992fcd9fbf101659178277```라고 입력하면 된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/594b8386-9221-476f-bdc6-fba51321bf5c)

'detached HEAD' state라고 나온다. 여기서 ```git branch```를 입력하면 다음과 같이 출력된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/c022b8c3-2897-4ae4-aca2-661c7c8df362)

main을 가리키지 않고 1194611이라는 커밋 아이디를 가리키고 있는 것이다. 즉 branch가 아니라고 할 수 있다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/5d8a8be7-0445-4f0e-ba68-7c4bed434383)

여기서 내부적으로 보면 위와 같이 바뀐 파일은 HEAD 파일 하나이다. 지금 HEAD는 직접 커밋 아이디를 가리키고 있다. 이렇게 되면 지금은 어떤 특정한 branch에 속해 있는 것이 아니라, HEAD가 특정 커밋을 직접 가리키고 있는 것이다. detached 되어 있는 상태인 것이다.

이제 터미널에서 ```git checkout main```으로 다시 main branch로 체크아웃 하면, 아래와 같이 gistory에서 HEAD는 이제 다시 refs/heads/main을 가리키고 있는 것을 볼 수 있다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/36fd224a-e784-4840-8665-da1d335f7bcf)

