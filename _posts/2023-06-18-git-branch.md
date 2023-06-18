---
layout: post
title: branch 만들기
description: 지옥에서 온 Git 섹션 3. branch 만들기
categories: Git
date: 2023-06-18 10:58:00 +0900
---
branch를 다루기 위해 gitfh2 내의 예제를 전부 지웠다. 그리고 다시 ```git init```을 하여 gitfh2 디렉토리에 저장소를 만든다.

```vim f1.txt```를 하여 내용을 "a"라고 쓰고 저장 및 빠져나가기를 한다. ```git add f1.txt```를 입력하여 버전 관리를 시작하고, ```git commit -m "1"```을 입력해서 커밋 한다.

한 번 더 수정한다. ```vim f1.txt```를 하고 "b"를 추가한다. 이번에는 ```git commit -am "2"```를 하여 add 및 commit을 한꺼번에 한다.

(버전 관리가 되지 않은 파일 즉, 아직 한 번도 add를 하지 않은 파일은 ```git commit -a ~~~```를 하였을 때 자동으로 add 되지 않는다.)

이제 ```git log```를 하면 현재 두 개의 버전이 생성된 것을 알 수 있다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f3a1d668-822f-4767-ac73-fa886b7a603b)

이제 branch를 생성해볼 것이다.

우선 ```git branch```라고 입력하면 다음과 같이 출력된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/de1702ea-d9d1-4979-a46e-f6cf77b8d904)

'\* main'이란 현재 내가 main브랜치를 사용하고 있다는 것이다. 깃은 내가 깃을 사용하는 순간부터 기본 branch를 사용하게 되어 있다. main branch는 일종의 약속 같은 특별한 이름의 branch이다.

새로운 branch를 만들려면 ```git branch 브랜치명```을 입력해야 한다. 이번에는 'exp'라는 이름의 branch를 만들 것이다.

```git branch exp```를 입력한 다음, ```git branch```를 입력하면 다음과 같이 출력된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/20b83d4f-12fb-492f-9fe7-259ae15f5b45)

현재 main branch를 사용하고 있기 때문에 별표가 main 앞에 있다. 다른 branch로 들어가려면 ```git checkout 브랜치명```을 입력하면 된다. ```git checkout exp```를 하고 ```git branch```를 입력하면 다음과 같이 출력된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/d143cdd9-bba0-4e7c-a5c6-c27d3acc03fe)

이제 별표가 exp 앞에 있다.

```git log```을 입력하면 다음과 같이 출력된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0644f379-6bad-44a0-b69e-a1b33d202eb7)

main branch와 exp branch는 같은 형태인 것을 알 수 있다. 내가 branch를 생성하면, 생성한 branch는 현재 내가 속해 있는 branch를 그대로 갖게 된다.

exp branch에서 ```vim f1.txt```를 입력하고 "c"를 추가한 다음, 저장 및 빠져나가기를 한다. 그리고 ```git add f1.txt``` 및 ```git commit -m "3"```을 입력한다. 그리고 ```git log```을 입력하면 다음과 같이 출력된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/14694a8b-815e-4891-8a5b-adb31cb096f6)

1, 2, 3을 가지고 있는 것이다.

이 상태에서, ```git checkout main```을 하고, ```git log```를 하면 다음과 같이 출력된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/33ad2aa2-30d4-491f-8aea-655d107a374c)

1, 2만 나타나는 것이다. 파일의 내용을 읽기 위해 ```cat f1.txt```를 입력하면 다음과 같이 출력된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1395b8c5-ac34-420c-9200-73e20255e76d)

a, b만 나타난다.

다시 exp branch로 체크아웃 하고, ```cat f1.txt```를 하면 

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/aeb1788e-1d98-42d9-aa18-f00693158b79)

이렇게 a, b, c가 나타난다. 현재 내가 어느 branch에 있는가에 따라서 내용이 완전 달라지는 것이다.
