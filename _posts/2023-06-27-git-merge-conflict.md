---
layout: post
title: merge & conflict
description: 지옥에서 온 Git 섹션 5. merge & conflict
categories: Git
date: 2023-06-27 14:38:00 +0900
---
Git은 branch를 효율적으로 다룰 수 있게 하는 혁신을 가져왔다. branch가 활성화됨에 따라 수많은 충돌(conflict)이 발생했다. 이번에는 충돌이 일어났을 때 깃은 내부적으로 어떤 변화를 갖게 되는지 알아볼 것이다.

우선 gitfh2 디렉토리를 git init으로 초기화한다. 그리고 f1.txt 파일을 생성해 다음과 같은 내용으로 저장 및 git add, git commit을 한다.

<img width="201" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/632666a7-e2b1-426b-916a-3f4926e422ac">
