---
layout: post
title: 배열 자료구조의 특징과 파이썬에서의 리스트
description: FastCampus 개발자 취업 합격 패스 Chapter 04. 배열
date: 2023-04-01 13:50:00 +09:00
categories: Data-Structure
---
**배열(Array)**이란, 데이터를 나열하고 각 데이터를 인덱스에 대응하도록 구성한 데이터 구조이다.

파이썬에서는 리스트 타입으로 배열 기능이 제공된다.

배열이 필요한 이유는 다음과 같다.

* 같은 종류의 데이터를 효율적으로 관리하기 위해 사용
* 같은 종류의 데이터를 순차적으로 저장


#### 배열의 장단점

* 장점
  * 빠른 접근 가능. 첫 데이터의 위치에서 상대적인 위치로 데이터 접근 (인덱스 번호로 접근)

* 단점
  * 데이터 추가 / 삭제의 어려움. 미리 최대 길이를 지정해야 함


배열의 경우 미리 최대 길이가 지정해야 해서 데이터를 추가하거나 삭제하는 것이 복잡하다는 특징을 가진다. 아래의 C 언어로 배열을 구현한 것을 ㅂ면,

```c
#include <stdio.h>

int main(int argc, char * argv[])
{
  char country[3] = "US";
  printf ("%c%c\n", country[0], country[1]);
  printf ("%s\n", country);
  return 0
}
```

이렇게 된다. 배열의 길이를 3으로 미리 지정해둔 것이다.

한편, 파이썬으로 배열을 구현하는 것은 C 언어에 비해 매우 단순하다.

```python
country = 'US'  # 파이썬에서 문자열은 내부적으로 배열을 사용하고 있다.

country = country + 'A'
print(country)
```

이처럼, 파이썬은 다른 많은 프로그래밍 언어들과는 다르게 배열을 구현할 때 미리 배열의 길이를 지정하지 않아도 된다는 특징을 지닌다.

파이썬에서 리스트(list)로 배열을 생성할 수 있는데, 리스트는 동적이고 사이즈가 변경될 수 있다.

즉, 리스트에서 필요한 요소들을 추가하거나 제거할 수 있고, 그러면 리스트는 그에 부합하게 자동으로 리사이즈(resize) 한다는 것이다.

```python
data_list = []

data_list.append(1)
data_list.append(2)
data_list.append(3)

data_list.remove(2)

print(data_list)  # [1, 3]
```

위 코드에서처럼, 처음에 길이가 정해져 있지 않은 비어 있는 리스트를 생성하고, append, remove 등으로 유동적으로 내부의 요소들을 추가하거나 제거할 수 있는 것이다.
