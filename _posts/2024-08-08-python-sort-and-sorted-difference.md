---
layout: post
title: sort vs sorted
categories: Python
description: https://blog.naver.com/wideeyed/221745416992
date: 2024-08-08 21:10:00 +0900
---
파이썬에서 리스트를 정렬할 때에는 sort 함수와 sorted 함수를 사용할 수 있다. 이 두 함수의 차이점에 대해 알아보자.

## sort 함수

sort 함수의 경우 ```리스트명.sort()``` 의 방식으로 사용한다. "리스트 객체의 메서드" 이다.

<b>원본 리스트를 직접 수정</b>한다. 그리고 <b>반환 값은 None</b> 이다.

예시 코드는 다음과 같다.

```python
lst = [9, 3, 6]
print('--- 정렬 전 ---')
print('lst:', lst)

lst2 = lst.sort()  # 원본을 정렬하고 수정한다.
print('--- 정렬 후 ---')
print('lst:', lst)
print('lst2:', lst2)
```

위 코드의 출력 결과는 다음과 같다.

<img src="https://github.com/user-attachments/assets/cfee580c-6e52-43a9-ac2b-582d82c1cf60" width="400px">

<br>

위 결과를 통해 알 수 있는 중요한 점은, sort 함수를 사용한 경우 함수의 리턴값이 None 이라는 것이다.

정렬된 값은 리턴되지 않는다. 단지 원본 리스트의 값이 정렬된 값으로 변경된 것이다.

## sorted 함수

sorted 함수는 ```sorted(리스트명)``` 의 형식으로 "내장 함수" 이다. (sort 함수가 "리스트형의 메서드"인 것과 구별된다.)

sorted 함수를 사용한 경우 <b>리스트의 원본 값은 그대로 유지</b>되고, <b>정렬된 값을 반환한다.</b>

예시 코드는 다음과 같다.

```python
lst = [9, 3, 6]
print('--- 정렬 전 ---')
print('lst:', lst)

lst2 = sorted(lst)  # 원본은 유지하고 정렬한 새 리스트를 만든다.
print('--- 정렬 후 ---')
print('lst:', lst)
print('lst2:', lst2)
```

위 코드의 출력 결과는 다음과 같다.

<img src="https://github.com/user-attachments/assets/fe077dae-2d1a-4428-bde1-3bf7ba0ff695" width="400px">

<br>

원본 리스트 lst 의 값은 유지되고, 정렬된 새 리스트는 lst2 에 저장되었음을 알 수 있다.

## sort 함수와 sorted 함수 비교 정리

### 1. 원본 데이터에 미치는 영향

- ```sort()``` : 원본 리스트가 변경된다.
- ```sorted()``` : 원본 데이터는 변경되지 않고, 정렬된 새로운 리스트가 반환된다.

### 2. 사용 가능 객체

- ```sort()``` : 리스트에만 사용할 수 있다.
- ```sorted()``` : 리스트뿐만 아니라 튜플(tuple), 문자열(string), 딕셔너리(dictionary), 집합(set) 등 모든 반복 가능한(iterable) 객체에 사용할 수 있다.

### 3. 속도와 성능

- ```sort()``` : 메서드 호출에 따른 오버헤드가 적기 때문에 같은 작업을 수행할 때 sorted() 보다 약간 더 빠를 수 있다.
- ```sorted()``` : 더 많은 유연성을 제공하지만, 리스트를 복사하는 추가 작업이 필요하기 때문에 sort() 보다 느릴 수 있다.

### 4. 정렬 옵션

- 두 함수 모두 'key'와 'reverse' 인자를 지원한다.
    - ```key``` : 정렬 기준이 되는 함수 또는 람다 표현식을 지정한다.
    - ```reverse``` : 'True'로 설정하면 내림차순으로 정렬된다.

## 결론

원본 리스트를 직접 정렬하고 싶을 때에는 sort() 를 사용한다. 원본 리스트를 유지하면서 정렬된 새로운 리스트가 필요할 때는 sorted() 를 사용한다.