---
layout: post
title: 문자열 뒤집기 문제
description: 이것이 취업을 위한 코딩 테스트다 with 파이썬 p.313
categories: Algorithm
date: 2023-11-19 22:40:00 +0900
---
문자열 뒤집기 문제의 경우 한 번에 연속된 문자열을 한 번씩만 뒤집는 것이니, 입력받은 문자열 s를 처음부터 해서 0에서 1로 바뀌거나, 1에서 0으로 바뀔 때 끊어서 새로운 리스트에 담으면 된다.

가령, '0001100'을 입력받는다면, '000', '11', '00'으로 구분하는 것이다.

어차피 0과 1로만 구성되어 있는 것이기 때문에, 결국 새로운 리스트의 길이를 2로 나눈 몫의 횟수만 뒤집기를 실행해주면 된다. 최소 횟수를 구하는 것이기 때문에, 0으로 시작하는 것이나 1로 시작하는 것 중에서 적거나 같은 것이 최소 횟수이다.

답안 코드는 다음과 같다.

```python
s = input()
listA = []
cont_str = s[0]
for i in range(1, len(s)):
    if s[i] == s[i - 1] or cont_str == '':
        cont_str += s[i]
    else:
        listA.append(cont_str)
        cont_str = ''
        cont_str += s[i]

listA.append(cont_str)

result = len(listA) // 2
print(result)
```

<br>

입력 예시

```
0001100
```

출력 예시

```
1
```