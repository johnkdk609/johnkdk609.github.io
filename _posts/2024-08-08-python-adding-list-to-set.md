---
layout: post
title: 파이썬 리스트(List)를 집합(Set)에 넣기
categories: Python
date: 2024-08-08 19:31:00 +0900
---
파이썬으로 알고리즘 문제를 풀다 보면, 집합(Set) 자료구조를 사용해야 할 때가 있다.

주로 중복된 것들을 손쉽게 제거하기 위해 집합을 사용하는데, 경우에 따라 중복되는 리스트들을 제거하고 싶을 때가 있다.

<br>

<b>그런데 리스트(list)는 집합(set)에 직접 담을 수 없다.</b>

<br>

이는 <u>list 가 변경 가능한(mutable) 객체이기 때문</u>이다. Python의 set은 내부적으로 요소들의 해시 값을 사용하여 중복을 제거하고, 요소의 순서와 상관없이 빠르게 검색할 수 있게 설계되어 있다.

그러나 list는 mutable 객체로, 그 값이 변경될 수 있다. 해시(hash) 값은 객체의 상태에 의존하는데, list와 같은 mutable 객체는 상태가 변할 수 있기 때문에 해시 값이 변할 수 있다.

이런 이유로 Python에서는 list와 같이 mutable한 객체는 set의 요소로 사용할 수 없다.

<br>

예시 코드는 다음과 같다.

```python
aset = set()
alst = [1, 2, 3]

aset.add(alst)  # 오류 발생
```

발생하는 오류는 다음과 같다.

<img src="https://github.com/user-attachments/assets/8f431897-550c-4cf4-9359-29d9298576f2" width="800px">

<br>

'TypeError: unhashable type: 'list'' 오류가 발생한다.

<br>

## 해결 방법

set에 담고 싶은 데이터가 list 형태라면, 변경 불가능한 객체(immutable object)로 변환할 수 있다. 

예를 들어 <b>tuple은 immutable하기 때문에 set의 요소로 사용할 수 있다.</b>

<br>

예시 코드는 다음과 같다.

```python
aset = set()
alst = [1, 2, 3]
atuple = tuple(alst)

aset.add(atuple)  # 정상 작동
print(aset)
```

<br>

<img src="https://github.com/user-attachments/assets/4746f751-c27e-419a-b4ab-4d8f1789f984" width="550px">

성공적으로 되었다. 이렇게 tuple로 변환한 후에는 set에 담을 수 있다.