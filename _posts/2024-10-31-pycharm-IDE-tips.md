---
layout: post
title: 파이참 IDE 사용 꿀팁
categories: Algorithm
date: 2024-10-31 22:00:00 +0900
---
파이썬으로 알고리즘 문제를 풀 때 사용하는 파이참(Pycharm) IDE의 꿀팁들에 대해 알아보자.

## 현재 파일 실행 단축키

파이참 IDE를 켜면 실행(Run) 이 무조건 main.py 를 향하고 있다. 만약 내가 temp.py 라는 새 창을 만들고 그 창에서 실행 시키고자 한다면, <kbd>Ctrl + Shift + F10</kbd> 을 사용하면 된다.

<h3><b>Ctrl + Shift + F10</b></h3>

그냥 <kbd>Shift + F10</kbd> 을 하면 '직전에 실행시킨 파일'이 실행된다. 이 경우에는 temp.py 파일이 아닌, main.py 파일이 실행되는 것이다.

심지어 시험장에서 파이참을 켰을 때에는 마우스로 'Select Run/Debug Configuration' 를 클릭했을 때 'Current File' 이라는 버튼이 없었다. 그래서 많이 당황했던 기억이 있다. 이때 <kbd>Ctrl + Shift + F10</kbd> 단축키를 클릭했더라면 main.py 가 아닌 내가 실행시키고 싶은 다른 파일이 실행되었을 것이다.

습관적으로 <kbd>Ctrl</kbd> 키를 붙여서 실행시켜야 한다.

<br>

## Redirect Input

