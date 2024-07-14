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

### 자료형 - 숫자

```type()``` 함수를 사용하면 자료형을 쉽게 확인할 수 있다.

```python
print(type(6))  # 정수

# <type 'int'>
```

```python
print(type('A'))    # 문자열

# <type 'str'>
```

파이썬에서 숫자를 나타내는 자료형으로 정수(```int```), 부동소수점수(```float```), 복소수(```complex```)가 있다.

정수의 경우, 너무 길어서 읽기 힘들면 밑줄을 넣어도 된다.

```python
100_000_000     # 세 자리마다
1_0000_0000     # 네 자리마다
```

복소수(```complex```)의 경우 생소하다. 제곱하면 -1이 되는 수 i를 '허수(imaginary number)'라고 한다.

```i² = -1```

파이썬에서는 허수 i를 ```j```로 나타낸다.

```python
print((1j) ** 2)

# (-1+0j)
```

```python
print((1 + 1j) ** 10)

# 32j
```

### 자료형 - 시퀀스

문자열(str), 리스트(list), 튜플(tuple), 사용자 정의 클래스가 시퀀스에 속한다.

```python
print(type("Love your Enemies, for they tell you your Faults."))

# <class 'str'>
```

```python
print(type(['love', 'enemy', 'fault']))

# <class 'list'>
```

```python
print(type(('love', 'enemy', 'fault')))

# <class 'tuple'>
```

for 문에서 사용할 수 있는 것들이 바로 시퀀스이다. 문자열은 시퀀스에 속한다. 여러 개의 문자를 한 줄로 세워뒀기 때문이다.

### 자료형 - 매핑

딕셔너리(dict)는 키(key)와 값(value)의 짝으로 이뤄진다. 이런 것을 매핑이라고 한다.

```python
print(type({'one': 1, 'two':2, 'three':3}))

# <class 'dict'>
```

### 자료형 - 불

참, 거짓을 표현하는 불(bool)도 있다.

```python
print(type(False))

# <class 'bool'>
```

```python
print(type(3 >= 1))

# <class 'bool'>
```

```python
print(type(True == 'True'))

# <class 'bool'>
```

### 자료형 - 세트

집합을 표현하는 세트(set)도 있다.

```python
fruits = {'apple', 'banana', 'orange'}
```

세트는 원소의 순서가 유지되지 않고 중복 원소를 갖지 않는 '집합'으로서의 특징이 있으며, 집합 연산을 사용할 수 있다.

### 문자열의 글자 바꾸기 - 불가능

```python
x = 'banana'
``` 

'banana'를 'nanana'로 바꿀 수 있을까?

```python
x[0] = 'n'
```

위와 같이 하면 안 된다. 아래처럼 TypeError 오류가 발생한다.

```
Traceback (most recent call last):
  File "/Users/kdk/Documents/dev_study/coding_test_python/practice.py", line 3, in <module>
    x[0] = 'n'
    ~^^^
TypeError: 'str' object does not support item assignment
```

이렇듯, 문자열에 들어있는 글자는 바꿀 수 없다.

아래처럼 할 수는 있겠다.

```python
x = 'n' + x[1:]
print(x)

# 'nanana'
```

이것은 문자열 x의 'b'를 'n'으로 바꾼 것이 아니라, 'n'과 'anana'를 합친 새로운 문자열 'nanana'에 x라는 이름을 붙인 것이다.

### 문자열 - find()

문자열에 어떤 글자가 몇 번째 자리에 있는지 알고 싶을 때는 ```find()```를 사용하면 된다.

```python
s = "hello Python!"
print(s.find('P'))

# 6
```

'P'가 6번 인덱스에 있다는 것을 알았으니, 다음과 같이 슬라이싱해서 다른 변수로 저장할 수도 있다.

```python
h = s[0:6]
print(h)

# 'hello '
```

위의 h 변수의 끝에는 공백이 포함되는데, 다음과 같이 슬라이싱을 하거나 ```rstrip()```으로 제거할 수 있다.

```python
print(h.rstrip())

# 'hello'
```

또는 다음과 같이 주어진 문자열을 분할한 리스트를 생성하는 split()을 이용해 첫 번째 단어를 알아내는 방법도 있다.

```python
print(s.split())

# ['hello', 'Python!']

print(s.split()[0])

# 'hello'
```

### 리스트 - insert, del, pop

리스트에서 원소를 삽입하려면 insert, 삭제하려면 del 또는 pop을 사용하면 된다.

```python
prime = [3, 7, 11]

prime.append(5)
# [3, 7, 11, 5]

prime.sort()
# [3, 5, 7, 11]

prime.insert(0, 2)
# [2, 3, 5, 7, 11]

del prime[4]
# [2, 3, 5, 7]

a = prime.pop()     # 삭제한 원소를 a 변수로 받음
print(prime)
# [2, 3, 5]
print(a)
# 7

prime[0] = 1    # 리스트의 원소에 새로운 값 지정
print(prime)
[1, 3, 5]
```

원소를 삭제할 때 pop()을 사용할 수 있다. pop()은 리스트에서 삭제한 원소를 반환(return)하므로 변수로 받아서 나중에 사용할 수도 있다.

<br>

다음과 같이 영화들의 리스트가 있다고 해보자.

```python
movie_list = ['닥터 스트레인지', '스플릿', '럭키', '배트맨']
```

이때 '슈퍼맨' 영화를 '닥터 스트레인지'와 '스플릿' 사이에 추가하려면 다음과 같이 해야 한다.

```python
movie_list.insert(1, '슈퍼맨')
```

insert 메서드를 쓸 때에는 먼저 순서를 인자로 쓰고 그 다음에 넣을 것을 입력해야 한다.

### 슬라이싱 - replace()

replace() 메서드를 써서 특정 단어를 다른 단어로 바꿀 수도 있다.

```python
h = 'Hello world!'

h.replace('world', 'Python')
print(h)

# 'Hello Python!'
```

<br>

다음 코드의 실행 결과를 예상해보자.

```python
>> string = 'abcd'
>> string.replace('b', 'B')
>> print(string)
```

이때 replace 메서드가 사용되었으니 'aBcd'가 출력될 것이라고 생각할 수 있다.

하지만 이것은 틀렸다. 정답은 'abcd' 이다.

왜냐하면 앞서 알아봤듯이 문자열은 변경할 수 없는 자료형이기 때문이다. replace 메서드를 사용하면 원본은 그대로 둔 채로 변경된 새로운 문자열 객체를 리턴해준다.

<br>

### join 메서드

예를 들어 아래의 interest 리스트에는 데이터들이 바인딩되어 있다.

```python
interest = ['삼성전자', 'LG전자', 'Naver', 'SK하이닉스', '미래에셋대우']
```

위 interest 리스트를 사용하여 다음과 같이 출력하려면 어떻게 해야 할까?

```
삼성전자 LG전자 Naver SK하이닉스 미래에셋대우
```

그러려면 join 메서드를 사용해야 한다.

```python
print(" ".join(interest))
```

join 메서드를 응용하여 다양하게 출력할 수 있다.

```python
interest = ['삼성전자', 'LG전자', 'Naver', 'SK하이닉스', '미래에셋대우']
print("/".join(interest))
```

```
삼성전자/LG전자/Naver/SK하이닉스/미래에셋대우
```

```python
interest = ['삼성전자', 'LG전자', 'Naver', 'SK하이닉스', '미래에셋대우']
print("\n".join(interest))
```

```
삼성전자
LG전자
Naver
SK하이닉스
미래에셋대우
```

### bin() 함수

파이썬의 bin() 함수를 이용하면 이진수를 쉽게 구할 수 있다.

```python
print(bin(13))

# '0b1101'
```

```0b```는 뒤의 숫자가 이진수임을 나타낸다.

### 튜플(tuple)

다음 코드를 보자.

```python
c = 10
d = 20
c, d = d, c
print(c, d)

# 20 10
```

위 코드 세 번째 줄에서 등호 왼쪽은 c, d라는 변수가 담긴 튜플이고, 오른쪽은 d, c의 값이 담긴 튜플이다. 그래서 d는 c로 들어가고, c의 값은 d로 들어간다. 이런 일들이 차례차례 일어나는 것이 아니고, 동시에 처리된다.

함수에서 튜플이 요긴하게 쓰이기도 한다. 아래 함수는 인자(매개변수)를 주는 대로 받아먹는 함수이다.

```python
def magu_print(x, y, *rest):
    print(x, y, rest)

magu_print(1, 2, 3, 5, 6, 7, 9, 10)

# 1 2 (3, 5, 6, 7, 9, 10)
```

위 함수는 인자를 두 개 이상만 주면 나머지는 알아서 다 처리한다. 함수를 정의할 때 인자에 별표를 붙여두면 그 이후에 들어오는 것은 모두 튜플에 집어넣는 것이다. 위에서는 (3, 5, 6, 7, 9, 10)가 하나의 튜플로 묶였다.

이제 튜플의 문법을 보겠다.

```python
t = ('a', 'b', 'c')
```

튜플을 만들 때는 위와 같이 괄호를 써도 되고 안 써도 된다. 다만, 원소가 없는 튜플을 만들 때는 괄호를 꼭 써야 한다.

```python
empty = ()
```

원소를 하나만 가진 튜플을 만들 땐 원소 뒤에 콤마(,)를 꼭 찍어야 한다.

```python
one = 5,
print(one)

# (5,)
```

그리고 튜플은 리스트와 달리 원소값을 직접 바꿀 수 없기 때문에, 문자열에서 했던 것처럼 오려붙이는 방법을 써야 한다.

```python
p = (1, 2, 3)
q = p[:1] + (5,) + p[2:]
print(q)

# (1, 5, 3)

r = p[:1], 5, p[2:]
print(r)

# ((1,), 5, (3,))
```

튜플을 리스트로, 리스트를 튜플로 쉽게 바꿀 수도 있다.

```python
p = (1, 2, 3)
q = list(p)
print(q)

# [1, 2, 3]

r = tuple(q)
print(r)

# (1, 2, 3)
```

<br>

튜플에는 별 표현식(star expression)이 있다. 기본적으로 데이터 언패킹은 좌변의 변수와 우변 데이터 개수가 같아야 한다. 하지만 star expression을 사용하면 변수의 개수가 달라도 데이터 언패킹을 할 수 있다.

튜플에 저장된 데이터 중에서 앞에 있는 두 개의 데이터만 필요할 경우 나머지 데이터의 언패킹 코드를 작성할 필요가 없다.

```python
>> a, b, *c = (0, 1, 2, 3, 4, 5)
>> a
0
>> b
1
>> c
[2, 3, 4, 5]
```

<br>

별 표현식을 활용한 문제를 보자. 10개의 값이 저장된 scores 리스트가 있을 때, star expression을 사용하여 좌측 8개의 값을 valid_score 변수에 바인딩하려면 다음과 같이 해야 한다.

```python
scores = [8.8, 8.9, 8.7, 9.2, 9.3, 9.7, 9.9, 9.5, 7.8, 9.4]
*valid_score, _, _ = scores
print(valid_score)
```

```
[8.8, 8.9, 8.7, 9.2, 9.3, 9.7, 9.9, 9.5]
```

### 딕셔너리(dict)

딕셔너리 자료형은 키(key)와 값(value)의 쌍으로 이루어진다.

딕셔너리 자료형은 아래와 같이 사용할 수 있다.

```python
dic = {}    # dic이라는 이름으로 비어있는 딕셔너리 생성

dic['dictionary'] = '1. A reference book containing an ...'
dic['python'] = 'Any of various nonvenomous snake of the ...'

print(dic['dictionary'])

# '1. A reference book containing an ...'
```

또, 딕셔너리 자료형은 해싱(hashing) 기법을 이용하기 때문에 자료가 순서대로 저장되지 않는다.

해싱 기법은 자료를 아주 빨리 찾을 수 있는 방법이다.

딕셔너리 자료형에서 원소를 삭제할 때는 다음과 같이 하면 된다.

```python
smalldic = {'dictionary': 'reference', 'python': 'snake'}

del smalldic['dictionary']

print(smalldic)

# {'python': 'snake'}
```

이번에는 family라는 딕셔너리를 만들어 보겠다.

```python
family = {'mom': 'Kim', 'dad': 'Choi', 'baby': 'Choi'}
```

여기서 family의 키들을 얻으려면 딕셔너리 이름 뒤에 ```.keys()```를 쓰면 된다.

```python
print(family.keys())

# dict_keys(['mom', 'dad', 'baby'])
```

family의 값들을 얻으려면 딕셔너리 이름 뒤에 ```.values()```를 쓰면 된다.

```python
print(family.values())

# dict_values(['Kim', 'Choi', 'Choi'])
```

family의 원소(키/값 쌍)들을 얻으려면 이름 뒤에 ```.items()```를 쓰면 된다.

```python
print(family.items())

# dict_items([('mom', 'Kim'), ('dad', 'Choi'), ('baby', 'Choi')])
```

딕셔너리에 어떤 키가 있는지 없는지는 in을 써서 알아볼 수 있다. 있으면 True, 없으면 False라고 답해 준다.

```python
print('dad' in family)

# True

print('sister' in family)

# False
```

### ord()와 chr()

ord() 함수는 문자에 해당하는 코드값을 알려준다.

```python
ord('A')
# 65

ord('Z')
# 90

ord('a')
# 97

ord('z')
# 122

ord('0')
# 48

ord('9')
# 57
```

역으로, chr() 함수에 코드값을 입력으로 넣으면 그에 해당하는 문자를 얻는다.

```python
chr(65)
# 'A'
```

한글에 대해서도 두 함수를 사용할 수 있다.

```python
ord('가')
# 44032

chr(55197)
# 힝
```

### split()과 splitlines()

split() 은 공백을 기준으로 문자열을 분리한 것들을 리스트에 넣어 반환한다.

```python
print('hello world'.split())

# ['hello', 'world']
```

여러 행으로 이뤄진 문자열을 분리하려면 ```\n```을 구분자로 지정한다.

```python
love = '''L is for the way you look at me
... O is for the only one I see
... V is very, very extraordinary
... E is even more than anyone that you adore can'''

print(love.split('\n'))

# ['L is for the way you look at me', 'O is for the only one I see', 'V is very, very extraordinary', 'E is even more than anyone that you adore can']
```

위와 같이 줄바꿈을 기준으로 문자열을 분할할 때는 ```splitlines()```를 써도 된다.

### 세트(set)

세트(set)는 '집합'을 표현하는 것이다.

다음과 같이 fruits 세트가 있다.

```python
fruits = {'apple', 'banana', 'orange'}
```

여기서, 망고를 추가하려면 ```add()```를 하면 된다.

```python
fruits.add('mango')
print(fruits)

# {'orange', 'apple', 'mango', 'banana'}
```

이번에는 회사 이름을 나타내는 집합을 만들어보겠다.

```python
companies = set()
companies.add('apple')
companies.add('microsoft')
companies.add('google')
```

세트를 이용해 다음과 같이 집합 연산을 사용할 수 있다.

```python
print(fruits & companies)   # 교집합
# {'apple'}

print(fruits | companies)   # 합집합
# {'apple', 'mango', 'microsoft', 'orange', 'google', 'banana'}
```

아래와 같이 여러 세트를 리스트에 담은 뒤 set의 메서드를 쓸 수도 있다.

```python
list_of_sets = [fruits, companies]
print(set.intersection(*list_of_sets))   # 교집합
# {'apple'}

print(set.union(*list_of_sets))  # 합집합
# {'google', 'apple', 'banana', 'mango', 'microsoft', 'orange'}
```

apple은 fruits에도 속하고 companies에도 속하는데, 위 합집합의 결과에 한 번만 나오는 것을 볼 수 있다. 이와 같이 세트는 중복 원소를 갖지 않는다. 또, 원소의 순서가 유지되지 않는 특징도 있다.

```python
alphabet = list('google')
print(alphabet)
# ['g', 'o', 'o', 'g', 'l', 'e']

print(set(alphabet))
# {'e', 'o', 'g', 'l'}
```

또, 집합끼리 뺄셈도 할 수 있다.

```python
S1 = {1, 2, 3, 4, 5, 6, 7}
S2 = {3, 6, 9}
print(S1 - S2)

# {1, 2, 4, 5, 7}
```

### 모듈 - math, calendar, tkinter

수학적인 계산이 필요하다면 math라는 모듈을 불러와서 사용하면 된다.

```python
import math

print(math.sqrt(2))     # 2의 제곱근
# 1.4142135623730951

print(math.pi)  # math 모듈의 변수 pi(원주율)의 값은?
# 3.1415926535897931

print(math.hypot(3, 4))     # 피타고라스 정리 a² + b² = c²
# 5.0
```

달력도 불러올 수 있다.

```python
import calendar
calendar.prmonth(2024, 1)

#     January 2024
# Mo Tu We Th Fr Sa Su
#  1  2  3  4  5  6  7
#  8  9 10 11 12 13 14
# 15 16 17 18 19 20 21
# 22 23 24 25 26 27 28
# 29 30 31
```

tkinter(티 케이 인터) 모듈을 활용해 창을 띄울 수도 있다.

```python
import tkinter as tk

s = "Life is short\nUse Python"

root = tk.Tk()
t = tk.Text(root, height=2, width=13)
t.insert(tk.END, s)
t.pack()
tk.mainloop()
```

어떤 모듈에 무엇이 있는지 살펴보려면, ```dir(모듈명)```을 입력하면 된다.

또, 어떤 모듈에 대한 설명을 보고 싶으면 ```help(모듈명)```을 입력하고 실행하면 된다.

### 모듈 - sys, os, re, webbrowser

sys 모듈은 파이썬 인터프리터를 제어할 수 있는 방법을 제공한다.

파이썬 인터프리터를 띄우면 우리의 명령을 기다린다는 뜻으로 ```>>>```를 표시하고 있다. (프롬프트)

sys 모듈을 사용하면 이 프롬프트를 바꿀 수 있다.

```python
>>> import sys
>>> sys.ps1                                  # 현재의 프롬프트는?
'>>> '
>>> sys.ps1 = '^^; '                         # 요걸로 바꿔!
^^; print('hello')
hello
^^; 5 * 3
15
^^;
```

인터프리터를 끝내려면 다음과 같이 하면 된다.

```python
^^; sys.exit()
```

os 모듈은 운영체제(OS)를 제어할 수 있다.

```python
import os

print(os.getcwd())  # 현재 경로 알아내기. current working directory

# /Users/kdk/Documents/dev_study/coding_test_python

print(os.listdir())     # 어떤 파일들이 있는지 알아보기
```

re 모듈의 경우 정규 표현식(regular expression)을 이용해 문자열을 다룰 수 있다. 마침표(.)는 문자 아무거나 한 개를 뜻하고, 별표(*)는 0개 이상의 문자를 뜻한다.

webbrowser 모듈의 경우 다음과 같이 사용할 수 있다.

```python
import webbrowser

url = 'http://www.python.org/'
webbrowser.open(url)
```

실행시키면 파이썬 페이지가 열린다.

### 랜덤(random) 모듈

파이썬으로 난수를 만들려면 다음과 같이 하면 된다.

```python
import random

print(random.random())

# 0.12094978595791062
```

random 모듈의 random() 함수를 호출했더니 복잡한 숫자를 돌려주었다. random() 함수는 0 이상 1 미만의 숫자 중에서 아무 숫자나 하나 뽑아서 돌려주는 일을 한다.

주사위처럼 1에서 6까지의 정수 중 하나를 무작위로 얻으려면 randrange() 라는 함수를 사용하면 된다.

```python
import random

print(random.randrange(1, 7))

# 4
```

"1 이상 7 미만의 난수"라고 생각하면 이해가 쉽다.

shuffle() 이라는 함수도 있다. 시퀀스를 뒤죽박죽으로 섞어놓는 함수이다.

```python
import random

abc = ['a', 'b', 'c', 'd', 'e']
random.shuffle(abc)
print(abc)
# ['c', 'd', 'a', 'b', 'e']
```

아무 원소나 하나 뽑아주는 choice() 함수도 있다.

```python
import python

abc = ['a', 'b', 'c', 'd', 'e']
print(random.choice(abc))
# 'd'

menu = '쫄면', '육개장', '비빔밥'
print(random.choice(menu))
# '쫄면'
```

참과 거짓 중에 하나를 뽑고 싶다면 다음과 같이 하면 된다.

```python
print(random.choice([True, False]))
# True
```

### string 모듈

```python
import string

print(string.ascii_uppercase)   # 대문자 A~Z
# 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'

print(string.ascii_lowercase)   # 소문자 a~z
# 'abcdefghijklmnopqrstuvwxyz'

print(string.ascii_letters)     # 소문자와 대문자 모두 갖고 있음
# 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'

print(string.digits)    # 숫자
# '0123456789'
```

### 객체지향 - 클래스와 인스턴스

파이썬은 꼭 객체지향적으로 작성하지 않아도 된다. 하지만 객체지향에 대해 이해하고 나면 파이썬으로 윈도우 프로그래밍을 하거나, 복잡한 프로그램을 작성하는 데 많은 도움이 된다.

클래스가 '부류'라면, 인스턴스는 '실체'이다. 실제 세계에 존재하는 실체(instance)를 객체(object)라고 하고, 객체들의 공통점을 간추려서 개념적으로 나타낸 것이 클래스(class)인 것이다.

'사과'가 클래스라면, '내가 어제 먹은 그 사과'는 인스턴스이다. '좋은 집'이 클래스라면, '우리 집'은 인스턴스이다.

파이썬으로 부류와 실체를 표현해 보겠다.

```python
class Singer:               # 가수 정의
    def sing(self):         # 노래하기 메서드
        return "Lalala~"

taeji = Singer()            # 태지 생성
print(taeji.sing())         # 태지씨 노래 한 곡 부탁해요~
# 'Lalala~'

ricky = Singer()
print(ricky.sing())
# 'Lalala~'
```

### 객체지향 - self

self라는 것은 바로 그 클래스의 객체를 가리킨다. jane과 mary가 똑같은 attack 메서드를 가지기 때문에 서로 구별하기 위해 사용한 것이다. 한마디로, 메서드를 정의할 때는 항상 self라는 인자를 써준다고 생각하면 된다. self를 어떻게 사용하는지 좀 더 살펴보기 위해 Amazon 클래스에 메서드를 추가해보겠다.

```python
class Amazon:
    strength = 20
    dexterity = 25
    vitality = 20
    energy = 15

    def attack(self):
        return "Jab!!!"

    def exercise(self):     # 추가한 메서드
        self.strength += 2
        self.dexterity += 3
        self.vitality += 1

jane = Amazon()
mary = Amazon()
eve = Amazon()

print(jane.strength)
# 20
print(jane.attack())
# 'Jab!!!'
print(mary.strength)
# 20
print(mary.attack())
# 'Jab!!!'

eve.exercise()      # eve를 훈련시킨다.
print(eve.strength)
# 22
```

eve를 exercise 시키고 나니 힘(strength)이 세진 것을 볼 수 있다.