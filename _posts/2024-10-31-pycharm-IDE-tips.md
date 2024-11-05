---
layout: post
title: 파이참 IDE 사용 꿀팁
categories: Algorithm
date: 2024-10-31 22:00:00 +0900
---
파이썬으로 알고리즘 문제를 풀 때 사용하는 파이참(Pycharm) IDE의 꿀팁들에 대해 알아보자.

## 현재 파일 실행 단축키

파이참 IDE를 켜면 실행(Run) 이 무조건 main.py 를 향하고 있다. 만약 내가 temp.py 라는 새 창을 만들고 그 창에서 실행 시키고자 한다면, <kbd>Ctrl + Shift + F10</kbd> 을 사용하면 된다.

<p style="font-size: large"><b>Ctrl + Shift + F10</b></p>

그냥 <kbd>Shift + F10</kbd> 을 하면 '직전에 실행시킨 파일'이 실행된다. 이 경우에는 temp.py 파일이 아닌, main.py 파일이 실행되는 것이다.

심지어 시험장에서 파이참을 켰을 때에는 마우스로 'Select Run/Debug Configuration' 를 클릭했을 때 'Current File' 이라는 버튼이 없었다. 그래서 많이 당황했던 기억이 있다. 이때 <kbd>Ctrl + Shift + F10</kbd> 단축키를 클릭했더라면 main.py 가 아닌 내가 실행시키고 싶은 다른 파일이 실행되었을 것이다.

습관적으로 <kbd>Ctrl</kbd> 키를 붙여서 실행시켜야 한다.

<br>

## Redirect Input

문제를 풀다 보면 코드를 실행한 다음 주어진 테스트 케이스를 넣어서 결괏값을 확인하는 과정을 수차례 하게 된다. 

그때마다 문제의 하단으로 내려가서 테스트 케이스를 복사하고 옮겨붙이는 것은 비효율적이다. <kbd>Ctrl + Shift + F10</kbd>을 눌러 실행을 시키면 미리 준비시켜둔 입력값이 들어가 바로 결과가 나오게 할 수 있다. 바로 <b>Redirect Input</b> 을 사용하는 것이다.

우선 입력값을 준비시켜 놓을 txt 파일을 하나 생성한다. 가령 input.txt 라는 파일을 현재 프로젝트 바로 아래 단위에 생성한다. 그리고 원하는 테스트케이스를 input.txt 파일에 복사 + 붙여넣기 하고 저장해둔다.

이제 파이참 IDE 우측 상단에 '▼' 버튼을 누르면, 'Edit Configuations' 라는 버튼이 토글되어 보인다. 이것을 클릭하고, 화면에 뜬 Run/Debug Configurations 창에서 Execution > Redirect input from 체크박스를 체크 표시한다. 그리고 오른쪽에 파일 모양의 Browse 버튼을 클릭하고, 직전에 만든 input.txt 파일로 설정해둔다.

이제 문제를 풀다가 중간에 Unit Test 를 하는 과정 혹은, 다 풀고 나서 결괏값을 검증하는 등의 과정에서 <kbd>Ctrl + Shift + F10</kbd>을 클릭하여 실행하면 실행하는 즉시 특정 테스트 케이스에 대한 내 코드의 결괏값이 입력창에 나타난다.

Redirect Input 방식을 사용하여 불필요하게 소요되는 시간을 줄이고, 효율성을 높이는 것이다. 물론 <u>최종 제출을 하기 전에는 필수적으로 Redirect Input 기능을 해제하고, 주어진 테스트 케이스들을 전부 넣어보는 과정을 거쳐야 한다.</u>

<br>

## Compare With

알고리즘 문제에 대한 코드를 작성하다 보면, 새로운 파일을 만들어 원본 코드를 복사 + 붙여넣기 한 후 새로운 파일에서 이것저것 테스트를 하는 경우가 있다. 약간의 수정을 해야 할 필요를 느낄 때 그렇게 많이 한다.

새로 만든 임시 파일에서 코드를 수정하고 나면, 원본 코드와 비교해서 어떤 부분들을 변경했는지 파악하고 싶을 수 있다. 그런데 코드가 길고 복잡하면 정확히 어떤 부분을 수정했는지 파악하기 어렵다.

어럴 때 사용할 수 있는 것이 파이참 IDE의 <b>Compare With</b> 기능이다.

가령 원본 파일이 original.py 고 임시로 수정하던 파일이 temp.py 라면, temp.py 파일이 메인 화면인 상태에서 파이참 상단에 'View' 버튼을 클릭한다. 그리고 'Compare With...' 버튼을 클릭한 다음 temp.py 와 비교하고 싶은 original.py 파일을 선택해 클릭한다.

그러면 'temp.py - original.py' 라는 이름의 창이 생긴다. 한 화면에 양옆으로 temp.py, original.py 가 뜨는데, 변경된 사항의 경우 따로 초록색 등의 음영이 뜬다. 그리고 » 혹은 « 표시가 뜨는데, 가령 « 를 클릭하면 왼쪽에 없거나 오른쪽과 다른, 오른쪽의 특정 코드를 왼쪽에 적용하는 것이다. 이렇게 손쉽게 두 파일 간 다른 점을 비교하고 수정할 수 있다.

두 파일이 동일해지면 창 좌측 상단에 'Contents are identical' 이라는 문장이 나타난다.

<b>View > Compare With...</b> 기능을 사용하여 실수 없이 두 파일 간 동기화를 할 수 있는 것이다.

<br>

## Find Usages

알고리즘 문제를 풀다 보면 서로 다른 두 개 이상의 변수들의 변수명을 비슷하게 작성해 한 눈에 특정 변수들이 어디에 있는지 파악하기 어려울 수 있다. 또, 코드가 길고 복잡해 내가 어떤 변수에 대해 어디서 선언했고, 어디서 할당했는지를 파악하고 싶을 수 있다.

이때 사용할 수 있는 파이참 IDE 의 기능이 <b>Find Usages</b> 이다.

가령 <a href="https://www.codetree.ai/training-field/frequent-problems/problems/tail-catch-play/description?page=2&pageSize=20">Code Tree 꼬리잡기놀이</a> 문제에서 본인의 코드를 보겠다. 메인에서 grid를 생성하고, 그 grid 에 데이터를 받은 다음, 그 grid를 활용하여 특정한 작업을 수행하였다.

만약 grid 라는 변수가 어디에서 사용되었는지 한 눈에 파악하고 싶다면, grid 를 드래그 한 다음 마우스 우클릭을 한 다음 'Find Usages' 버튼을 클릭한다. 그러면 다음과 같은 화면이 나온다.

<img src="https://github.com/user-attachments/assets/6b377f5c-a090-44b5-9c50-2c9ae240fe5e" width="1000px" />

위 그림을 보면 'grid in All Places' 라는 창이 생기며 그 안에서 grid 변수가 어느 지점에서 사용되었는지를 파악할 수 있다.

이러한 Find Usages 기능의 단축키는 <kbd>Alt + F7</kbd> 이다.

<br>

## Find Usages in File

위에서 사용한 Find Usages 기능과 비슷한 기능으로, <b>Find Usages in File</b> 기능이 있다.

마찬가지로 특정 변수에 대해 코드에서 어디서 선언을 했고, 어디서 할당했는지 파악하고 싶으면 해당 변수에 마우스로 클릭 해두고, <kbd>Ctrl + F7</kbd> 단축키를 입력한다.

그러면 다음과 같은 화면이 나온다. (마찬가지로 Code Tree 꼬리잡기놀이 문제의 예시를 보겠다.)

<img src="https://github.com/user-attachments/assets/bdd2f624-a6fb-458c-b16d-39a1c5342a68" width="600px" />

위 사진을 보면, 변수가 선언된 경우에는 빨간색으로 음영이 생기고 값이 할당되는 경우에는 초록색으로 음영이 생긴다.

<img src="https://github.com/user-attachments/assets/c3294848-a4bf-46b5-94dc-e030b11e8e99" width="470px" />

또, 같은 변수에 대해 어떤 메서드에 인자로 넣어서 리턴값을 해당 이름으로 그대로 받을 때에는 위와 같이 되는 것을 볼 수 있다.

어떤 변수가 어디서 사용되었는지를 빠르게 파악하려면 <kbd>Ctrl + F7</kbd> 단축키를 사용하면 되는 것이다.

<br>

