---
layout: post
title: 파이썬 공부하면서 정리한 것들
description: 왕초보를 위한 Python - 쉽게 풀어쓴 기초 문법과 실습
date: 2023-12-29 23:26:00 +0900
---
파이썬을 공부하면서 정리할 만한 것들을 입력했다.

### 몫과 나머지를 한 번에 구하기

```divmod()``` 함수를 써서 몫과 나머지를 한 번에 계산할 수 있다.

```python
print(divmod(50, 8))

# (6, 2)
```

### 피타고라스 정리 a² + b² = c² 활용해서 직선 거리 구하기

a와 b의 길이를 알면 c의 길이를 알 수 있다.

루트값(양의 제곱근)을 계산해야 하는데, 파이썬에서는 math 모듈의 <b>sqrt 함수</b>를 이용하면 된다.

```python
import math
print(math.sqrt(a ** 2 + b ** 2))
```

### 리스트에서 remove()

리스트에서 값을 제거하려면 remove() 함수를 사용하면 된다.

```python
fruit = ['apple', 'banana', 'cherry', 'durian', 'pineapple']
fruit.remove('durian')

print(fruit)

# ['apple', 'banana', 'cherry', 'pineapple']
```

### 인터프리터와 컴파일러

스위치가 10개 있으면 2의 10제곱, 즉 1024가지 정보를 표현할 수 있다.

불이 켜지고 꺼진 것을 1과 0으로 나타내고, 스위치 하나에 해당하는 것을 <b>비트(bit)</b>라고 말한다. 컴퓨터에 일을 시키려면 컴퓨터가 알아들을 수 있는 말로 <b>지시(instruction)</b>를 내려야 한다.

이럴 때 쓰이는 프로그래밍 언어를 <b>저급 언어(low-level language)</b>라고 한다. 저급 언어로는 기계어와 어셈블리어가 있다. 그리고 사람이 프로그램 작성을 쉽게 할 수 있도록 C/C++, 파이썬, 자바 같은 <b>고급 언어(high-level language)</b>가 있다.

이런 고급 언어로 프로그램을 짠 다음에 컴퓨터가 알아들을 수 있게 번역을 해줄 필요가 있다. 그렇게 번역을 하는 방법에도 두 종류가 있다.

1. <b>인터프리트(interpret) 방식</b> - 한 마디 할 때마다 동시통역해주는 방식
2. <b>컴파일(compile) 방식</b> - 말하는 것을 처음부터 끝까지 듣고 나서 한꺼번에 바꿔주는 방식

파이썬은 우리의 명령을 한 줄씩 해석해서 일을 하는 인터프리트 방식이다. 사람이 파이썬 언어로 작성한 프로그램을 컴퓨터에 번역해주는 파이썬 셸이 바로 인터프리터(interpreter)이다.

### 반올림 함수 round()

round()는 반올림을 해주는 함수이다.

```python
print(round(1.23456, 2))    # 1.23456을 소수점 둘째 자리로(셋째 자리에서) 반올림

# 1.23

print(round(1.23456, 3))    # 1.23456을 소수점 셋째 자리로(넷째 자리에서) 반올림

# 1.235
```

참고로, ```round(2.675, 2)```를 수행하면 결과가 2.68이 아닌 2.67로 나오는데, 이것은 버그가 아니라 부동소수점(floating point) 연산의 한계이다.

```python
print(round(2.675, 2))

# 2.67
```

### and/or 연산 순서

파이썬에서는 and/or의 왼쪽 항을 먼저 평가(계산)하고, 오른쪽 항은 필요할 때만 평가한다. (대부분의 고급 언어에서 이렇게 한다.)

a와 b가 다음과 같이 있다고 하자.

```python
a = 3
b = 0
```

b 값이 0이므로 b를 분모로 나눗셈을 하면 다음과 같이 ```ZeroDivisionError```가 발생한다.

```
Traceback (most recent call last):
  File "/Users/kdk/Documents/dev_study/coding_test_python/practice.py", line 4, in <module>
    print(a / b)
          ~~^~~
ZeroDivisionError: division by zero
```

그런데 이 나눗셈 계산을 아래처럼 and의 오른쪽 항에 넣으면 오류가 생기지 않는다.

```python
print((a * b) > 0 and (a / b) > 0)

# false
```

파이썬이 '왼쪽 항을 평가해보니, 오른쪽 항은 평가할 필요가 없겠구나' 하고 넘어가버린 것이다.

위 문장의 순서를 바꿔서 나눗셈을 먼저 시켜보면 ```ZeroDivisionError```가 발생하는 것을 볼 수 있다.

```
Traceback (most recent call last):
  File "/Users/kdk/Documents/dev_study/coding_test_python/practice.py", line 4, in <module>
    print((a / b) > 0 and (a * b) > 0)
           ~~^~~
ZeroDivisionError: division by zero
```

### match-case 문

파이썬의 match-case는 다른 프로그래밍 언어의 switch-case와 비슷하기도 하지만 좀 더 강력한 기능을 가지고 있다.

우선 홀수, 짝수 판별의 예부터 들어보겠다.

```python
for n in range(1, 11):
    match n % 2:
        case 0:
            print(f"{n} is even.")
        case 1:
            print(f"{n} is odd.")
```

n을 2로 나눈 나머지가 0이면 짝수(even number), 1이면 홀수(odd number)라고 출력하는 것이다.

결과는 다음과 같다.

```
1 is odd.
2 is even.
3 is odd.
4 is even.
5 is odd.
6 is even.
7 is odd.
8 is even.
9 is odd.
10 is even.
```

여기까지만 보면 switch-case와 별반 다른 것이 없다고 생각할 수 있다. 하지만 여기서 끝이 아니다.

match-case를 이용해 피즈버즈(FizzBuzz) 문제를 풀어보겠다.

```python
for n in range(1, 101):
    match (n % 3, n % 5):
        case (0, 0):
            print("FizzBuzz")
        case (0, _):
            print("Fizz")
        case (_, 0):
            print("Buzz")
        case _:
            print(n)
```

위 코드의 실행 결과는 다음과 같다.

```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz
16
17
Fizz
19
Buzz
(이하생략)
```

여기서 ```match (n % 3, n % 5)```라는 구문을 볼 수 있는데, 소괄호로 둘러싸인 부분은 튜플이다. 괄호 안에 ```n % 3```과 ```n % 5```라는 두 개의 식이 있고, 그 아래에 ```case```에서 각각을 평가한다고 생각하면 된다.

```case (0, 0):```은 n을 3으로 나눈 나머지도 0이고, 5로 나눈 나머지도 0인 경우를 가리킨다. 따라서 'FizzBuzz'를 출력한다.

```case```문에서 밑줄(```_```)은 아무 값이나 상관없다는 뜻이다. 즉 ```case (0, _):```은 "n을 3으로 나눈 나머지가 0인 경우"이다. 따라서 "Fizz"를 출력한다.

마찬가지로 ```case (_, 0):```는 n을 5로 나누어 떨어지는 경우이고, 'Buzz'를 출력한다.

```case _:```는 그 밖의 모든 경우이다. 3으로도, 5로도 나누어 떨어지지 않는 숫자들이 이에 해당한다.

### for-else와 while-else

조건문에 else를 쓸 수 있듯이, 파이썬에서는 반복문에도 else를 쓸 수 있다.

```python
for x in [1, 2, 3, 4]:
    print(x)
else:
    print("리스트의 원소를 모두 출력했다.")
```

위 코드의 실행 결과는 다음과 같다.

```
1
2
3
4
리스트의 원소를 모두 출력했다.
```

마지막 출력을 굳이 else블록에 넣지 않고 반복문 바깥에 두어도 될 것 같다는 생각이 들 수 있다. 하지만 다음과 같이 break가 등장하면 얘기가 달라진다.

```python
for x in [1, 2, 3, 4]:
    if x % 3 != 0:
        print(x)
    else:
        break
else:
    print("리스트의 원소를 모두 출력했다.")
```

위 코드의 실행 결과는 다음과 같다.

```
1
2
```

여기서는 반복문을 break 했는데, 그럴 때에는 else 블록이 실행되지 않는다.

<br>

while문도 마찬가지이다. while문이 break될 경우에는 else 블록이 실행되지 않는다.

```python
countdown = 5
while countdown > 0:
    print(countdown)
    countdown -= 1
    if input() == "중단":
        break
else:
    print('발사!')
```

### 함수의 return문 활용

파이썬은 ```1 + 1 == 2```를 참이라고 답하고, ```1 + 1 == 3```에 대해서는 거짓이라고 답한다.

함수를 만들 때 이런 성질을 활용할 수 있다.

```python
def quiz():
    ans = input('1 + 2 = ')
    return 1 + 2 == int(ans)
```

위 코드의 실행 결과는 다음과 같다.

```
>>> quiz()
1 + 2 = 3
True
>>> quiz()
1 + 2 = 4
False
```

### 지역변수, 전역변수

* 지역변수: 함수 안에서 만들어진 변수
* 전역변수: 함수 밖에서 만들어진 변수

지역변수는 함수가 호출되면 만들어져서, 함수의 실행이 끝날 때 함께 없어지는 반면, 전역변수는 함수와는 관계없이 항상 존재한다. 영어로 전역변수는 global이라는 말로 표현한다.

지역변수를 함수 밖에서 한 번 불러보겠다.

```python
def d_is_10():
    d = 10
    print('d 값은', d, '입니다.')

print(d)
```

위 코드의 실행 결과는 다음과 같다.

```
Traceback (most recent call last):
  File "/Users/kdk/Documents/dev_study/coding_test_python/practice.py", line 5, in <module>
    print(d)
          ^
NameError: name 'd' is not defined. Did you mean: 'id'?
```

d를 불러봐도 'd라는 이름이 없다'는 에러 메세지만 뜬다. d_is_10() 함수가 실행되는 동안에는 d가 있었는데, 함수의 실행이 끝난 다음에 함께 사라져버렸기 때문이다. 반대로, 전역변수는 함수 안에서도 얼마든지 사용할 수 있다.

그런데 전역변수는 프로그램이 복잡해질수록 골칫거리가 된다. 다른 엉뚱한 함수 때문에 변수의 값이 바뀌어버리는 경우가 종종 있기 때문이다. 그래서 필요에 따라 지역변수와 전역변수를 골라 쓰는 것이 좋다.

그리고 함수 안에서 전역변수를 만드는 방법도 있다. 어떤 변수를 전역변수(global)로 사용하겠다고 명시해주는 것이다.

```python
def e_is_10():
    global e
    e = 10
    print('e 값은', e, '입니다.')

e_is_10()
print(e)
```

위 코드의 실행 결과는 다음과 같다.

```
e 값은 10 입니다.
10
```

여기서는 e_is_10() 함수가 실행되면서 e라는 전역변수가 만들어지고, 이 변수는 함수의 실행이 끝난 다음에도 없어지지 않는다.

### 람다(lambda)

람다(lambda)는 함수를 딱 한 줄만으로 만들게 해주는 기능이다.

```python
lambda 매개변수 : 표현식
```

다음은 두 수를 더하는 람다 표현식이다.

```python
print((lambda x, y: x + y)(10, 20))

# 30
```

함수가 이름조차 없다.

### map()에서 람다 활용

map() 함수는 함수와 리스트를 인자로 받는다. 그리고 리스트로부터 원소를 하나씩 꺼내서 함수를 적용시킨 다음, 그 결과를 새로운 리스트에 담아준다.

```python
print(list(map(lambda x: x ** 2, range(5))))

# [0, 1, 4, 9, 16]
```

위의 map 함수가 매개변수로 받은 함수는 ```lambda x: x ** 2```이고, 리스트로는 ```range(5)```를 받았다. map 함수는 리스트에서 원소를 하나씩 꺼내서 함수를 적용시킨 결과를 새로운 리스트에 받아주니까, 위의 예제는 0을 제곱하고, 1을 제곱하고, 2, 3, 4를 제곱한 것을 새로운 리스트에 넣어주는 것이다.

### reduce()에서 람다 활용

이번에는 reduce 함수를 알아보겠다.

```python
reduce(함수, 시퀀스)
```

형식은 위와 같다. 시퀀스(문자열, 리스트, 튜플)의 원소들이 누적적으로 함수에 적용시킨다.

```python
from itertools import reduce

print(reduce(lambda x, y: x + y, [0, 1, 2, 3, 4]))

# 10
```

위의 예제는 먼저 0과 1을 더하고, 그 결과에 2를 더하고, 거기에다 3을 더하고, 또 4를 더한 값을 돌려준다. 한마디로 전부 다 더하라는 것이다.

```python
print(reduce(lambda x, y: y + x, 'abcde'))

# 'edcba'
```

### filter()에서 람다 활용

filter 함수의 경우 다음과 같이 사용된다.

```python
filter(함수, 리스트)
```

파이썬의 filter는 리스트에 들어있는 원소들을 함수에 적용시켜서 결과가 참인 값들로 새로운 리스트를 만들어준다. 다음은 0부터 9까지의 리스트 중에서 5보다 작은 것만 돌려주는 예제이다.

```python
print(list(filter(lambda x: x < 5, range(10))))

# [0, 1, 2, 3, 4]
```

위의 예제는 다음과 같은 프로세스로 진행된다. 0부터 9까지의 리스트에서 숫자를 하나씩 꺼낸다. 그 숫자를 x라 하고, x < 5 가 '참'이면 살려준다. 살아남은 것들은 새로운 리스트에 넣어준다. 끝.

이번에는 홀수만 돌려주는 filter를 만들어보도록 하겠다.

```python
print(list(filter(lambda x: x % 2, range(10))))

# [1, 3, 5, 7, 9]
```

파이썬에서 '참'은 1이고 '거짓'은 0이다. 위의 filter 함수를 실행시키면, 0을 2로 나눈 나머지는 0이니까 람다 함수의 결과값은 0이고, 0은 '거짓'이니까 버려진다.

1을 2로 나눈 나머지는 1이니까 람다 함수의 결과값은 1이고, 1은 '참'이니까 통과한다. 이런 식으로 수행하면서 홀수만 돌려주게 되는 것이다.