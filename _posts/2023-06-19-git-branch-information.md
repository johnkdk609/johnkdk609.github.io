---
layout: post
title: branch 정보확인
description: 지옥에서 온 Git 섹션 3. branch 정보확인
categories: Git
date: 2023-06-19 22:49:00 +0900
---
branch를 만들었을 때, 현재 어떤 상황인지 판단하기 위한 여러 가지 방법들에 대해 알아볼 것이다.

gitfh2 디렉토리에서, main branch와 exp branch의 차이점을 알고 싶다면 ```git log --branches --decorate```를 입력하면 된다. 그러면 현재 자신이 체크아웃 되어 있는 branch 말고, 내 저장소에 있는 모든 branch에 대한 정보를 볼 수 있다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/df12386e-cb2d-4156-bedb-426a7be36bde)

위 사진에서 '(main)'은 main branch의 최신 커밋이 2라는 뜻이고, exp branch의 최신 커밋이 4라는 뜻이다. '(HEAD -> exp)'란 현재 내가 체크아웃 되어 있는 branch가 exp라는 뜻이다.

이것을 조금 더 고도화 시켜서 보고 싶으면, ```git log --branches --decorate --graph```를 입력하면 된다. 

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/d99e5f03-70b0-412a-b65d-713dc64a30b0)

이렇게 빨간색 줄이 생긴다. 이렇게 branch가 흘러왔다는 것을 뜻한다. 하지만 이것만으로는 graph의 효용이 보이지 않는다. main과 exp가 서로 커밋 상태가 각자의 길을 걷고 있을 때 그 효용이 드러난다.

터미널에서 ```git checkout main```을 입력해 main branch로 이동한다. 그리고 ```vim f3.txt```를 입력하고, 내용을 "a"로 저장한 다음, git add 및 커밋 메시지 "5"로 git commit을 한다.

이제 ```git log```를 하면 다음과 같이 출력된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/cb67c66c-703f-4c39-bfcf-5ee23d8b186a)

1, 2, 5만 나오는데, ```git log```는 기본적으로 현재 branch에 대한 커밋들만 보여주기 때문이다.

```git log --branches```를 하면 모든 branch에 대한 커밋들이 보여지지만, 뭐가 뭔지 판단하기 쉽지 않다. 그래서 ```git log --branches --decorate```를 입력한다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/8e2fb79f-1d5a-4c92-a3c7-ee6691d7d0c7)

현재 나는 main branch에 체크아웃 되어 있다고 나온다. 그리고 exp branch의 최신 커밋은 4라고 나와 있다.

터미널에 ```git log --branches --decorate --graph```를 입력하면, 이제야 효용이 드러난다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f1bbe37d-58be-4407-bab9-9485450ddcae)

exp branch는 최신 커밋이 4이고, 그 이전에는 3이었다. 그리고 2라는 커밋에서 비롯된 것이다. main branch는 5 이전에 2였다. 즉, main과 exp의 공통의 조상은 커밋 2인 것임을 알 수 있는 것이다.

```git log --branches --decorate --graph --oneline```라고 입력하면 다음과 같이 한 줄로 현재 상태를 알 수 있다. 훨씬 더 간결하게 전체적인 상황을 조망해볼 수 있는 것이다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/775276bc-aa49-4748-83c1-17809110ca01)

만약 버전과 버전 사이의 차이점을 확인하고 싶으면 다음과 같이 하면 된다.

```git log main..exp```를 입력하면 다음과 같이 출력된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/bffbb5b4-cb42-4d61-82c5-9a1a84f1505b)

즉, main에는 없고 exp에는 있는 것들인 3, 4를 알 수 있는 것이다.

반대로 exp에는 없고 main에는 있는 것을 비교해서 보고 싶다면, ```git log exp..main```을 입력하면 된다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/6dc94059-a216-406f-91b0-61a7b904d108)

만약에 소스 코드까지 필요하면, ```git log -p exp..main```를 입력하여 알아낼 수 있다. 

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1fa0a126-2fa9-498b-a759-9410070353b1)

exp에는 없고 main에 있는 커밋이 "5"라는 것과, exp에는 파일이 없는데 main에서는 'f3.txt'라는 파일이 생성됐고, 그 내용이 "a"라는 것을 한 번에 알 수 있는 것이다.

이제 ```git diff main..exp```를 사용하면 각각의 branch의 현재 상태를 알 수 있다.

![image](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/69e6cec6-50ac-4b59-a5f4-c513b8d2b69f)

이런 식으로 각각의 branch 사이의 차이점을 파악할 수 있다.
