---
layout: post
title: 해시 테이블(Hash Table) 이해 및 구현
categories: Data-Structure
date: 2023-04-02 17:13:00 +09:00
description: FastCampus 개발자 취업 합격 패스 Chapter 09. 해시 테이블
---
해시 테이블(Hash Table)이란 키(Key)에 데이터(Value)를 저장하는 데이터 구조이다.

Key를 통해 바로 데이터를 받아올 수 있으므로, 속도가 획기적으로 빨라진다는 장점이 있다.

파이썬 딕셔너리(Dictionary) 타입이 해시 테이블의 예로, 파이썬에서는 해시를 별도로 구현할 필요가 없다. Key를 가지고 바로 데이터(Value)를 꺼낼 수 있는 것이다.

보통 배열로 미리 Hash Table 사이즈만큼 생성 후에 사용한다. (공간과 탐색 시간을 맞바꾸는 기법)


#### 주요 용어들

* 해시(Hash): 임의 값을 고정 길이로 변환하는 것
* 해시 테이블(Hash Table): 키 값의 연산에 의해 직접 접근에 가능한 데이터 구조
* 해싱 함수(Hashing Function): Key에 대해 산술 연산을 이용해 데이터 위치를 찾을 수 있는 함수
* 해시 값(Hash Value) 또는 해시 주소(Hash Address): Key를 해싱 함수로 연산해서 해시 값을 알아내고, 이를 기반으로 해시 테이블에서 해당 Key에 대한 데이터 위치를 일관성 있게 찾을 수 있음
* 슬롯(Slot): 한 개의 데이터를 저장할 수 있는 공간
* 저장할 데이터에 대해 Key를 추출할 수 있는 별도 함수도 존재할 수 있음

![https://www.fun-coding.org/00_Images/hash.png](https://user-images.githubusercontent.com/88493727/229341098-82e8bbca-2d6c-4c4d-9d82-48caf16ca84f.png "https://www.fun-coding.org/00_Images/hash.png")


#### 해시 테이블의 장단점

* 장점
  * 데이터 저장/읽기 속도가 빠르다. (검색 속도가 빠르다.)
  * 해시는 키에 대한 데이터가 있는지(중복) 확인이 쉽다.
* 단점
  * 일반적으로 저장공간이 좀 더 많이 필요하다.
  * 여러 키에 해당하는 주소가 동일할 경우 충돌을 해결하기 위한 별도 자료구조가 필요하다. (Chaining, Linear Probing)


#### 해시 테이블의 주요 용도

* 검색이 많이 필요한 경우
* 저장, 삭제, 읽기가 빈번한 경우
* 캐시 구현 시 (중복 확인이 쉽기 때문)


#### 충돌(Collision) 해결 알고리즘 (좋은 해시 함수 사용하기)

> 해시 테이블의 가장 큰 문제는 충돌(Collision)의 경우이다. 이 문제를 충돌(Collision) 또는 해시 충돌(Hash Collision)이라고 부른다.

충돌을 해결하는 데에는 대표적으로 두 가지 방법이 있다.
1. Chaining 기법
2. Linear Probing 기법


#### Chaining 기법

* 개방 해싱(Open Hashing) 기법: 해시 테이블 저장 공간 외의 공간을 활용하는 기법
* 충돌이 일어나면, 링크드 리스트라는 자료 구조를 사용해서, 링크드 리스트로 데이터를 추가로 뒤에 연결시켜서 저장하는 기법


Chaining 기법을 활용한 코드는 다음과 같다.

```python
import hashlib

hash_table = [0 for i in range(8)]

def get_key(data):
    hash_object = hashlib.sha256()
    hash_object.update(data.encode())
    hex_dig = hash_object.hexdigest()
    return int(hex_dig, 16)

def hash_function(key):
    return key % 8

def save_data(data, value):
    index_key = get_key(data)
    hash_address = hash_function(index_key)
    if hash_table[hash_address] != 0:
        for index in range(len(hash_table[hash_address])):
            if hash_table[hash_address][index][0] == index_key:
                hash_table[hash_address][index][1] = value
                return
        hash_table[hash_address].append([index_key, value])
    else:
        hash_table[hash_address] = [[index_key, value]]


def read_data(data):
    index_key = get_key(data)
    hash_address = hash_function(index_key)

    if hash_table[hash_address] != 0:
        for index in range(len(hash_table[hash_address])):
            if hash_table[hash_address][index][0] == index_key:
                return hash_table[hash_address][index][1]
        return None
    else:
        return None
```

위 코드를 보면, 유명한 해시 알고리즘인 SHA(Secure Hash Algorithm, 안전한 해시 알고리즘)을 사용해서 get_key 메서드를 생성했다. 어떤 데이터도 유일한 고정된 크기의 고정값을 리턴해주므로, 해시 함수로 유용하게 활용할 수 있다.

이 코드를 실행해보면,

```python
print(hash_function(get_key('db'))) # 1
print(hash_function(get_key('da'))) # 2
print(hash_function(get_key('dh'))) # 2

save_data('da', '01200123123')
save_data('dh', '33333333333')
print(read_data('dh'))  # 33333333333
```

'da', 'dh'의 해시 테이블로 들어가는 값이 2로 동일한 상황에서, chaining 기법이 활용되어 링크드 리스트로 잘 생성되고 'dh'의 데이터를 읽어오는 것이 문제 없이 이루어졌다.


#### Linear Probing 기법

* 폐쇄 해싱(Close Hashing) 기법: 해시 테이블 저장 공간 안에서 충돌 문제를 해결하는 기법
* 충돌이 일어나면, 해당 hash address의 다음 address부터 맨 처음 나오는 빈 공간에 저장하는 기법.
  * 저장 공간 활용도를 높이기 위한 기법


Linear Probing 기법을 활용한 코드는 다음과 같다.

```python
import hashlib

hash_table = [0 for i in range(8)]

def get_key(data):
    hash_object = hashlib.sha256()
    hash_object.update(data.encode())
    hex_dig = hash_object.hexdigest()
    return int(hex_dig, 16)

def hash_function(key):
    return key % 8

def save_data(data, value):
    index_key = get_key(data)
    hash_address = hash_function(index_key)
    if hash_table[hash_address] != 0:
        for index in range(hash_address, len(hash_table)):
            if hash_table[index] == 0:
                hash_table[index] = [index_key, value]
                return
            elif hash_table[index][0] == index_key:
                hash_table[index][1] = value
                return
    else:
        hash_table[hash_address] = [index_key, value]

def read_data(data):
    index_key = get_key(data)
    hash_address = hash_function(index_key)

    if hash_table[hash_address] != 0:
        for index in range(hash_address, len(hash_table)):
            if hash_table[index] == 0:
                return None
            elif hash_table[index][0] == index_key:
                return hash_table[index][1]
    else:
        return None
```

마찬가지로 작동시켜보면,

```python
print(hash_function(get_key('db'))) # 1
print(hash_function(get_key('da'))) # 2
print(hash_function(get_key('dh'))) # 2

save_data('da', '01200123123')
save_data('dh', '33333333333')
print(read_data('dh'))  # 33333333333
```
이렇게 잘 작동한다.


#### 시간 복잡도

* 일반적인 경우 (Collision이 없는 경우) **O(1)**
* 최악의 경우 (Collision이 모두 발생하는 경우) **O(n)**

> 해시 테이블의 경우, 일반적인 경우를 기대하고 만들기 때문에, 시간 복잡도는 O(1)이라고 말할 수 있다.
