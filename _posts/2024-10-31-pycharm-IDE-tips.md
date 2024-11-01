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

