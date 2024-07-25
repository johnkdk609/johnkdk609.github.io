---
layout: post
title: .copy() 와 copy.deepcopy()
categories: Python
date: 2024-07-25 22:01:00 +0900
---
파이썬에서 1, 2차원 배열 등을 사용하다 보면 임시로 배열을 복사해서 수정한 다음 원본을 수정하는 등의 행위를 자주 하게 된다.

이렇게 파이썬에서 객체를 복사할 때에는 ```.copy()``` 와 ```copy.deepcopy()``` 가 사용된다. 그런데 이 두 방식에는 큰 차이가 있다.

## 1. .copy()

* <b>얕은 복사(shallow copy)</b>를 수행한다.
* 1차원 배열이나 리스트 같은 경우 요소들의 복사본을 생성하지만, 요소들이 참조하고 있는 하위 객체는 복사되지 않고 원본 객체를 참조한다.
* 예를 들어, 2차원 리스트의 경우 상위 리스트는 복사되지만 하위 리스트들은 원본 리스트의 참조를 그대로 가진다.

## 2. copy.deepcopy()

* <b>깊은 복사(deep copy)</b>를 수행한다.
* 객체와 그 객체가 참조하는 모든 하위 객체들까지 재귀적으로 복사한다.
* 따라서 <u>복사된 객체는 원본 객체와 완전히 독립적으로 동작한다.</u>

<br>

이를 명확히 이해하기 위해 다음 예시를 보겠다.

```python
import copy

original = [[1, 2, 3], [4, 5, 6]]

# 얕은 복사
shallow_copied = original.copy()

# 깊은 복사
deep_copied = copy.deepcopy(original)

# 원본을 수정
original[0][0] = 99

print("Original:", original)
print("Shallow Copy:", shallow_copied)
print("Deep Copy:", deep_copied)
```

위 코드를 실행하면, 원본 리스트를 수정했을 때 얕은 복사와 깊은 복사가 어떻게 다르게 작동하는지 알 수 있다.

```
Original: [[99, 2, 3], [4, 5, 6]]
Shallow Copy: [[99, 2, 3], [4, 5, 6]]
Deep Copy: [[1, 2, 3], [4, 5, 6]]
```

얕은 복사(shallow copy)를 한 'shallow_copied' 리스트는 원본 리스트의 상위 리스트만을 복사한다. 따라서 원본 리스트의 하위 리스트를 수정하면 얕은 복사본도 영향을 받는다.

반면 깊은 복사(deep copy)를 한 'deep_copied' 리스트는 원본 리스트와 그 하위 리스트들까지 모두 독립적으로 복사한다. 따라서 원본 리스트의 수정이 깊은 복사본에 영향을 미치지 않는다.

<br>

## copy.deepcopy() 에 대한 견해

알고리즘 문제풀이를 하다 보면 2차원 배열 등을 깊은 복사를 하는 경우가 자주 발생한다.

이때 copy 라이브러리의 deepcopy() 를 사용하면 매우 간단히 깊은 복사를 수행할 수 있다.

하지만 이 방법은 직접 2차원 배열을 생성하여 옮겨 적는 것보다 속도가 더 오래 걸릴 수 있다.

'copy.deepcopy()'는 객체를 재귀적으로 깊은 복사하는 함수로, Python의 모든 내장 타입과 사용자 정의 객체를 처리할 수 있도록 설계되었다. 따라서 2차원 배열과 같은 간단한 구조에서도 복사해야 할 객체의 타입과 구조를 파악하고 적절한 복사 방법을 적용하는 과정에서 오버헤드가 발생한다.

반면, 직접 2차원 배열을 복사하는 방법은 배열의 구조를 명확히 알고 있을 때 훨씬 단순하고, 불필요한 오버헤드 없이 빠르게 수행될 수 있다. 예를 들어, 이중 루프를 사용하여 2차원 배열을 복사하는 방법은 매우 효율적일 수 있다.

<br>

다음 예시를 보자.

```python
import copy
import time

# 크기가 1000x1000인 2차원 배열을 생성한다.
original = [[i for i in range(1000)] for j in range(1000)]

# copy.deepcopy()를 사용하여 배열을 복사한다.
start_time = time.time()
deep_copied = copy.deepcopy(original)
deep_copy_time = time.time() - start_time

# 이중 루프를 사용하여 배열을 복사한다.
start_time = time.time()
manual_copy = [[original[i][j] for j in range(1000)] for i in range(1000)]
manual_copy_time = time.time() - start_time

deep_copy_time, manual_copy_time
```

위 코드는 두 가지 방법의 실행 시간을 비교한다. 'copy.deepcopy()'를 사용하는 방법과 직접 배열을 복사하는 방법이다. 이 코드를 실행하면 두 방법의 속도 차이를 알 수 있다.

위 코드의 실행 결과는 다음과 같다.

* 'copy.deepcopy()'를 사용하여 배열을 복사하는 데 걸린 시간 : 약 0.492초
* 직접 이중 루프를 사용하여 배열을 복사하는 데 걸린 시간 : 약 0.103초