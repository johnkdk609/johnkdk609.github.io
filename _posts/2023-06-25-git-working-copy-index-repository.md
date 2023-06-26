---
layout: post
title: working copy & index & repository
description: 지옥에서 온 Git 섹션 5. working copy & index & repository
categories: Git
date: 2023-06-25 23:33:00 +0900
---
git reset이라는 명령어를 사용할 때 옵션이 몇 가지 있다. ```git reset --help```를 입력하면, ```--soft```, ```--mixed```, ```--hard``` 등이 보인다. 이 세 가지가 주로 많이 사용된다.

깃의 굉장히 중요한 내부 구조인 'working directory', 'index' 그리고 'repository'라는 세 가지 구조에 대해 이해해볼 것이다.

<table>
  <tr>
    <th>working directory<br>working tree<br>working copy</th>
    <th>index<br>staging area<br>cache</th>
    <th>repository<br>history<br>tree</th>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td align="center">git reset --soft</td>
  </tr>
  <tr>
    <td></td>
    <td colspan="2" align="center">git reset --mixed</td>
  </tr>
  <tr>
    <td colspan="3" align="center">git reset --hard</td>
  </tr>
</table>

우선, 세 가지 요소가 있다. 첫 번째는 working directory로 실제 작업을 진행하는 곳이다. 그리고 이것을 git add를 했을 때 포함되는 곳이 index이다. 그리고 버전들 즉 커밋들이 저장되는 곳이 respository이다. (repository라는 표현은 굉장히 광범위하게 사용된다. 그런데 이렇게 세 가지의 요소가 언급된 경우에 repository는 커밋된 버전들이 저장되는 곳을 뜻한다.)

working directory와 같은 뜻의 다른 명칭으로는 working tree, working copy가 있다. index는 staging area, cache 라고도 불린다. repository는 history, tree라고도 불린다.

git reset는 각각 옵션을 무엇을 주는가에 따라 삭제되는 대상이 달라진다. ```git reset --hard```는 세 영역 전부 초기화 된다. ```git reset --soft```의 경우 respository만 초기화 되고 나머지는 건드리지 않는다. ```git reset --mixed```는 index와 repository는 취소하지만 working directory는 건드리지 않는다.

이제 실습을 해보겠다. gitfh2 파일을 초기화하고, git init을 한다. 그리고 f1.txt라는 파일을 열어 처음에 "init"이라는 내용으로 저장하고, 커밋 메시지 1로 커밋한다. 다음으로 다시 f1.txt를 열어 "init"을 지우고 "repository"를 적고 저장한다. 그리고 커밋 메시지 2로 커밋한다.

지금, working directory, index, repository 모두 f1.txt가 'repository'라는 내용으로 되어 있다.

<table>
  <tr>
    <th></th>
    <th>working directory</th>
    <th>index</th>
    <th>repository</th>
  </tr>
  <tr>
    <td>f1.txt</td>
    <td>"repository"</td>
    <td>"repository"</td>
    <td>"repository"</td>
  </tr>
</table>

```cat f1.txt```를 입력해 working directory를 확인할 수 있다. index의 경우 gistory에서 index 파일이 f1.txt에 대해 가리키고 있는 Object Id가 "repository"인 것으로 알 수 있다. 그리고 repository의 내용은 gistory의 refs/heads/main 파일을 통해 main branch가 가리키고 있는 가장 최근 커밋인 트리 정보를 보면서 알 수 있다.

<img width="840" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/0533284d-1b67-4591-9ed6-decd9026fa33">

<img width="836" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/1ef9073f-4707-4e00-b296-8b4a735f3f8f">

이 상태에서 f1.txt 파일을 열고 내용을 "repository"에서 "index"로 바꾸고 git add 만 하면 다음과 같은 상태가 된다.

<img width="423" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/8c52ce09-c846-48b9-a901-63c891d245e8">

f1.txt를 수정하고 git add 까지만 했으니 다음과 같은 상황이 되는 것이다.

<table>
  <tr>
    <th></th>
    <th>working directory</th>
    <th>index</th>
    <th>repository</th>
  </tr>
  <tr>
    <td>f1.txt</td>
    <td>"index"</td>
    <td>"index"</td>
    <td>"repository"</td>
  </tr>
</table>

이제 f1.txt 파일을 열어서 내용을 "index"에서 "working copy"로 수정하고 저장한다. 이제 다음과 같은 상태가 된다.

<table>
  <tr>
    <th></th>
    <th>working directory</th>
    <th>index</th>
    <th>repository</th>
  </tr>
  <tr>
    <td>f1.txt</td>
    <td>"working copy"</td>
    <td>"index"</td>
    <td>"repository"</td>
  </tr>
</table>

이렇게 저장한 내용들을 이전에 알아본 git reset --soft, --mixed, --hard와 한꺼번에 비교하기 쉽게 표로 나타내면 다음과 같다.

<table>
  <tr>
    <th></th>
    <th>working directory<br>working tree<br>working copy</th>
    <th>index<br>staging area<br>cache</th>
    <th>repository<br>history<br>tree</th>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td align="center">git reset --soft</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td colspan="2" align="center">git reset --mixed</td>
  </tr>
  <tr>
    <td></td>
    <td colspan="3" align="center">git reset --hard</td>
  </tr>
  <tr>
    <td>f1.txt</td>
    <td>"working copy"</td>
    <td>"index"</td>
    <td>"repository"</td>
  </tr>
</table>

이 상태에서 ```git reset --soft```를 하면 repository에 있는 내용은 취소가 되지만, working copy와 index에 있는 각각의 파일 내용은 취소가 되지 않는다. ```git reset --hard```를 하면 repository, index, working copy의 내용이 모두 취소된다. 그리고 ```git reset --mixed```를 하면 index와 repository의 내용만 취소가 된다.


## git reset --soft

```git log```을 했을 때,

<img width="507" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/dc783e9b-5655-4eef-b967-072594077ed2">

1번 커밋인 f9609fa7f4aef0b81a0f5c0665cabf4c00e24bb7 로 리셋을 하고 싶다면, ```git reset --soft f9609fa7f4aef0b81a0f5c0665cabf4c00e24bb7```를 입력하면 된다. 그리고 ```git log```를 입력하면 다음과 같이 출력된다.

<img width="515" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/20666ca8-2fbb-4bdf-9b0a-c8e57bd94902">

위와 같이 내용이 사라진 것을 볼 수 있다. 그리고 ```git log -p```를 입력하면 다음과 같이 출력된다.

<img width="504" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/88c6f4a4-9a35-4f3a-a71c-10c508ecab83">

f1.txt가 내가 첫 번째 커밋 했던 "init"이라는 텍스트를 가지고 있다.

<table>
  <tr>
    <th></th>
    <th>working directory</th>
    <th>index</th>
    <th>repository</th>
  </tr>
  <tr>
    <td>f1.txt</td>
    <td>"working copy"</td>
    <td>"index"</td>
    <td>"init"</td>
  </tr>
</table>

즉, ```git reset --soft```를 하면 repository의 내용만 리셋이 되는 것이다.

```git diff```를 입력하면 다음과 같이 나온다.

<img width="244" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/3906125d-f70c-4dd1-a1b9-c695aa47b8c5">

```git diff```는 working copy의 내용과 index의 내용을 비교하는 명령이다. f1.txt에 대해서 index에는 "index"라는 내용이 있고, working copy에는 "working copy"라는 내용이 있다고 나온다. 이를 통해 index와 working copy는 내용이 유지되고 있는 것을 볼 수 있다.


## git reset --mixed

일단 이전에 수행했던 ```git reset --soft```를 되돌릴 것이다. gistory를 보면, ORIG_HEAD가 있다. 깃은 어떤 위험한 명령이 수행되었을 경우 ORIG_HEAD에 이전 내용을 담아둔다. ORIG_HEAD를 통해서 과거로 돌아갈 수 있는 것이다.

```git reset --soft ORIG_HEAD```를 입력한다. 이때, 만약 ```git reset```에 --soft, --mixed, --hard를 붙이지 않으면 리셋을 하면서 기본적으로 --mixed가 동작한다. 이 경우 index의 내용도 "repository"로 바뀔 수 있는 것이다. 그래서 ```git reset --soft ORIG_HEAD```를 입력해야 하는 것이다.

이제 ```git log```를 입력하면 다음과 같이 출력된다.

<img width="511" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/2b0cde76-975a-47dd-aaf4-3e1298adebc2">

그리고 ```git reset --mixed f9609fa7f4aef0b81a0f5c0665cabf4c00e24bb7```를 입력한다. (--mixed는 생략할 수 있다.)

<img width="493" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/f5944598-91f6-4b8a-9356-6060d16a01c2">

이제 ```git diff```를 입력하면, 다음과 같이 출력된다.

<img width="251" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/6c448bc5-8ef4-4bfd-86e0-a723e077e627">

working copy의 내용은 그대로 "working copy"인데, index의 내용은 "init"으로 변했다.

즉, ```git reset --mixed```를 실행 시켰더니, 다음과 같이 변한 것이다.

<table>
  <tr>
    <th></th>
    <th>working directory</th>
    <th>index</th>
    <th>repository</th>
  </tr>
  <tr>
    <td>f1.txt</td>
    <td>"working copy"</td>
    <td>"init"</td>
    <td>"init"</td>
  </tr>
</table>


## git reset --hard

위와 같은 flow라면 ```git reset --hard```를 입력할 경우 working copy의 내용도 "init"으로 변할 것이라는 것을 알 수 있다.

만약 ```git reset --hard```라고 하고 뒤에 아무 것도 안 써주면 현재 나의 최신 커밋 상태로 하드 옵션이 적용돼서 working copy의 내용도 사라진다.

<img width="205" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/a8810f18-5368-4967-88c9-da94e7fa5679">

이제 ```cat f1.txt```를 입력하면, working copy의 내용이 "init"으로 바뀌어 있다.
