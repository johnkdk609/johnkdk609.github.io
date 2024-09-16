---
layout: post
title: 알고리즘 문제 풀 때 유의할 것들 정리
categories: Algorithm
date: 2024-08-30 20:39:00 +0900
---
알고리즘 문제를 풀면서 자주 하는 실수 및 유념해야할 것들을 정리해보겠다.

<br>

## 문제 제대로 읽기

정말 중요한 것이지만, 가끔 문제를 완전히 이해하지 않고 문제풀이를 시작할 때가 있다. 전체적으로 다 이해를 하고, 구상을 시작해야 하는데 읽는 순서대로 구상을 하는 것이다.

이렇게 하면 난이도가 쉬운 문제이거나 내가 운이 좋다면 정답을 한 번에 맞힐 수 있겠지만, 조금만 난이도가 올라가고 복잡해지면 앞서 한 구상을 뒤엎어야 하는 경우가 많이 발생한다. 시간을 낭비하는 것이다.

구상이 잘못된 점을 빨리 찾으면 그나마 다행이지만, 뒤늦게 찾으면 시간이 부족하다는 압박에 여태 구상, 구현한 것을 그대로 가져가고 수정을 하려 할 수 있다. 이렇게 되면 점점 미궁으로 빠지게 된다.

그러므로 <b>문제를 처음부터 끝까지 꼼꼼히 읽고 완전히 이해한 다음 구상, 구현을 시작해야 한다.</b>

<br>

## 테스트케이스 직접 손으로 검증하기

샘플 테스트케이스가 있을 때, 이것을 머릿속으로만 돌려보고 직접 손으로 검증해보지 않는 경우가 있다. 특히 구현(simulation) 문제의 경우 요구사항을 순차적으로 실행해야 하는 경우가 많다. 이때 테스트 케이스를 직접 손으로 A4 용지에 그려가며 단계별로 실행해보면, 내가 이해한 것이 올바른지 아니면 무언가 놓치고 있는 것이 있는지 파악하기 용이하다.

반례를 생각하기 어려운 경우가 있는데, 이 또한 손으로 직접 그려보면서 검증하면 반례를 찾기 쉬워진다. 이전에 풀었던 <a href="https://www.acmicpc.net/problem/3987">백준 3987 보이저 1호</a> 문제의 경우 손으로 직접 그려봤으면 찾을 수 있었을 법한 케이스를 머릿속으로만 판단하여 반례를 오랜 시간 못 찾은 적이 있다.

그러므로 <b>테스트 케이스를 직접 손으로 그려보며 검증해야 한다.</b>

<br>

## 문제 맨 아랫 부분까지 읽기

문제가 있고 테스트케이스가 여러 개 제시되어 있는 상황에서, 간혹 테스트케이스 맨 아랫 부분에 추가적인 설명이 있을 수 있다.

주어진 문제가 있는 페이지의 맨 아랫 부분까지 보지 않으면 테스트케이스들에 대한 면밀한 설명을 보지 못하고 갈 수 있다. <a href="https://www.acmicpc.net/problem/15685">백준 15685 드래곤 커브</a> 문제의 경우가 이러한 예시이다.

혼자서 문제의 지시대로 테스트케이스를 손으로 검증하더라도, 뭔가 이해가 잘 안 될 때가 있다. 이때 문제의 맨 아랫 부분에 있는, 테스트케이스에 대한 구체적인 설명을 보면 상당히 쉽게 이해할 수 있다.

그러므로 <b>문제의 맨 아랫 부분까지 스크롤을 내려보는 습관을 들여야 한다.</b>

<br>

## 메서드는 항상 마지막에 return 문으로 종료한다.

메서드를 사용할 때 편리한 점은 return 문을 사용해 메서드를 완전히 종료할 수 있다는 것이다. 

return문을 잘 사용함으로써 얻을 수 있는 효용은 다음과 같다.

* <b>결과값 전달</b> : return 문이 없으면 메서드는 None 을 반환한다. return 문을 사용하여 값을 반환하면, 이 값을 다른 메서드가 코드 블록에서 사용할 수 있다. 이를 통해 코드를 모듈화하고 재사용할 수 있다.

* <b>메서드 실행 종료</b> : return 문을 만나면 메서드의 실행이 즉시 종료되기 때문에, 특정 상황에서만 실행을 멈추게 하는 것이 가능하다. 또 조건에 따라 메서드를 일찍 종료하여 불필요한 추가 계산을 피할 수 있다.

* <b>코드 가독성 향상</b> : return 문을 적절히 사용하면 메서드가 어떤 결과를 반환하는지 명확해진다. 이는 코드의 가독성을 높인다. 또 메서드의 목적이 명확해진다.

* <b>상태 유지 방지</b> : return문을 사용하여 메서드를 설계하면, 메서드가 외부 상태를 변경하지 않고 입력된 인자만으로 결과를 반환하는 '<u>순수 함수</u>'로 만들 수 있다. 이는 코드의 예측 가능성을 높이고, 디버깅과 테스트를 더 쉽게 만든다.

* <b>재귀 함수에서 중요한 역할</b> : 백트래킹 문제와 같이 재귀 호출을 하는 경우, 종료 조건에서 return문을 사용한다. 그렇지 않으면 무한 루프에 빠지게 된다.

<br>

## 구상 단계에 들어가기 전에 시간 복잡도를 체크한다.

알고리즘 문제 풀이 과정을 크게 4단계로 구분한다면, <b>문제 분석 → 구상 → 구현 → 검증</b> 이라 할 수 있다. 각각의 단계에서 유의해야 할 것들이 있다.

그 중 하나는 문제 분석 단계에서 <u>시간 복잡도를 생각해봐야 한다</u>는 것이다.

어떤 문제를 풀 때에는 다양한 접근 방법이 존재할 수 있다. 어떤 접근 방법을 선택하는지는 다양한 요소가 고려될 수 있다. 어떤 자료구조를 선택하는지, 어떤 알고리즘을 선택하는지, 룩업 테이블(Look-up table)을 전부 입력하는 하드 코딩을 할 것인지, 간결한 코드를 짤 것인지 등.. 다양한 선택지가 있다.

이때 문제에서 주어진 조건을 잘 봐야 한다. 특히 주의 깊게 봐야 하는 것은 시간 제한은 몇 초인지, 메모리 제한은 몇 초인지이다.

만약 시간 제한이 짧게 주어져 있다면 시간 복잡도를 개선할 수 있는 방식으로 접근해야 하는 것이다. 그리고 리스트 등을 불필요하게 많이 생성해서 메모리 차원에서 비효율적으로 관리하고 있는 것은 아닌지도 고려해야 한다.

특히 백트래킹 문제의 경우, 시간 복잡도 체크가 필수이다. 만약 간당간당하다면 가지치기(pruning)를 얼마나 할 수 있을지 고민해봐야 한다.

가령 <a href="https://www.acmicpc.net/problem/15684">백준 15684 사다리 조작</a> 문제의 경우, 시간 복잡도에 대한 큰 고려 없이 코드를 구성했다가 계속 시간 초과로 실패하였다.

<br>

### 시간 복잡도를 개선할 때 사용할 수 있는 '소거법'

문제에서 다양한 값들의 범위를 알려준다. 이때 어떤 부분에서 내가 시간을 줄일 수 있는지 판단할 수 있어야 한다.

이때 사용할 수 있는 방법이 '소거법'이다.

<a href="https://www.acmicpc.net/problem/17143">백준 17143번 낚시왕</a> 문제를 보면서 소거법을 적용해보겠다.

격자판의 크기 R, C 의 범위는 2 ≤ R, C ≤ 100, 상어의 수 M의 범위는 0 ≤ M ≤ R×C 이다.

상어의 정보는 다섯 정수 r, c, s, d, z 로 이루어져 있는데, 각각의 범위는 다음과 같다. (1 ≤ r ≤ R, 1 ≤ c ≤ C, 0 ≤ s ≤ 1000, 1 ≤ d ≤ 4, 1 ≤ z ≤ 10000) s는 속력, d는 이동 방향, z는 크기이다.

이때 중요한 것은 <u>내가 건드릴 수 있는 부분과 건드릴 수 없는 부분을 구분하는 것</u>이다.

일단 격자의 크기가 최대 100 × 100 = 10000 의 크기로 테스트 케이스가 주어지는 것은 내가 통제할 수 있는 부분이 아니다.

상어의 수 M 은 최대 R × C 즉 10000 마리가 테스트케이스로 주어질 수 있는데 이 또한 내가 통제할 수 있는 부분이 아니다.

이제 s 를 봐야 한다. s 는 속력으로 이 수치만큼 상어가 이동하는 거리이다. s 라는 숫자 자체는 내가 통제할 수 없지만, 어떻게 처리하는가에 따라 천지차이의 결과를 도출할 수 있다.

만약 내가 한 칸, 한 칸 이동하는 방식을 사용하면, 최악의 경우 s 는 1000 으로, 악랄한 테스트 케이스가 존재한다면 시간은 O(C × M × s) 로 100 × 10000 × 1000 = 10⁹ 가 되고, 이렇게 되면 무조건 시간 초과가 발생한다.

s 즉 속력에 대해 더 스마트한 방식으로 처리해야 시간 초과를 예방할 수 있는 것이다.

이렇게 주어진 값들에 대해, 내가 통제할 수 없는 부분들을 소거하는 방식을 사용할 수 있다.

<br>

효율적인 코드를 짜겠다고 익숙하지 않은 방식으로 짜다가 꼬여서는 안 되겠지만, 시간 복잡도를 고려하지 않고 한참 구상·구현하다가는 나중에 결국 문제가 안 풀렸을 때 시간이 부족해 코드를 뒤엎기 매우 난처할 수 있다.

그러므로 <b>문제풀이 초반에 시간 복잡도에 대한 숙고를 통해 적합한 방법을 선택하는 것이 매우 중요하다.</b>

<br>

## 문제를 이해할 때 자의적인 해석은 절대 하지 않는다.

문제가 길거나, 요구사항이 상당히 복잡할 때 자주 저지르는 실수이다. 

빨리 구현을 시작해야겠다는 초조함에 문제의 조건들을 대충 읽고 일단 시작하는 경우가 있다. 해당 부분을 구현해야 할 때에야 비로소 조건을 들여다 보는 것이다.

특히 문제에서 테스트 케이스 예시를 그림으로 보여줄 때 대강 그림을 보고 어떻게 구현을 해야겠다는 판단을 내릴 때가 있다.

이것을 매우 경계해야 한다. 왜냐하면 구체적인 조건에 대한 보다 엄밀한 묘사는 문장으로 이루어지기 때문이다. 만약 그림 위주로 보고 (자의적인) 판단을 내린 다음, 바로 구현을 하면 높은 확률로 틀릴 것이다.

대표적으로 <a href="https://www.acmicpc.net/problem/23289">백준 23289 온풍기 안녕!</a> 문제를 풀 때 내가 이러한 실수를 저질렀다. 상, 하, 좌, 우 진행방향 각각에서 대각선으로 이동하는 경우가 있는데, 이것이 문제에 정확히 문장으로 조건이 적혀있음에도 그림으로 주어진 테스트 케이스 예시에만 너무 의존해 완전 잘못된 코드를 작성하고 있던 것이다.

그러므로 <b>문제의 문장을 엄밀히 읽고, 그림은 보조 수단으로 활용하며 자의적인 판단을 절대 하지 않아야 한다.</b>

<br>

## 문제가 잘 안 풀리면 잠깐 나갔다 온다.

문제를 풀다 보면 구상한 대로 구현하는 것이 어려울 때가 있다. 또 구현을 다 했는데 무언가 오류가 발생해 디버깅을 계속 해야 하는 경우가 있다. 구현이나 디버깅을 빠르게 성공하면 정말 좋겠지만, 가끔은 아무리 해도 안 되는 경우가 있다.

이때 잠깐 문제 푸는 것을 멈추고 밖에 나갔다 오는 것이 좋다. (잠시 나가는 것이 가능한 경우에만)

어떤 한 부분에 너무 매몰돼 있으면 잘못된 점이 잘 안 보일 수 있다. 현상에 대해 약간 거리를 두어야 보이는 것이 있는 법이다. 잠깐 밖에 나가서 (간식을 먹는 것이 가능한 경우에) 포도당 캔디, 간식 등을 먹고, 화장실도 다녀오고, 스트레칭도 하면서 머릿속에 가득차 있는 생각을 잠시 털어내는 것이다. (간식을 먹기 위해서는 주머니에 조그만한 간식을 넣어둬야겠다.)

그러면 다시 돌아왔을 때 안 보이던 것이 보이기 시작할 수 있다.

또 내 구상이 잘못된 것을 파악하고 리셋을 해야 하는 시점에 잠시 나갔다 오는 것도 좋다.

이렇게 물리적으로 스스로를 환기하지 않으면 새로운 구상을 하려 해도, 이전에 했던 구상이 계속 생각나 정작 제대로 된 구상을 하지 못할 수 있기 때문이다. 잠시 나갔다 오면서 다시 새로워진 정신으로 집중하는 것이 효율이 좋다.

그러므로 <b>문제가 잘 안 풀리거나 리셋을 할 때 잠시 나가서 바람을 쐬고 온다.</b>

<br>

## 메서드마다 단위 테스트 (unit test) 를 한다.

알고리즘 문제를 풀 때 메서드를 여러 개 만들어서 복합적으로 작용하게 해야 하는 경우가 많다. 이때 <u>메서드를 하나 만들 때마다 단위 테스트(unit test)를 해야 한다.</u>

단위 테스트란 코드의 가장 작은 기능적 단위를 테스트하는 프로세스이다. 단위 테스트는 코드 품질을 보장하는 데 도움이 되며, 소프트웨어 개발의 필수 요소이다.

메서드에 대한 단위 테스트 전략은 다음과 같다.

1. <b>논리 검사</b> - 프로그램이 올바른 계산을 수행하며 정확하고, 입력에 대해 정확한 출력을 내는가
2. <b>경계 검사</b> - 프로그램이 극단적인 경우 (edge case) 또는 잘못된 입력에 대해 어떻게 반응하는가
3. <b>오류 처리</b> - 입력에 오류가 있을 때 프로그램이 어떻게 반응하는가

실제로 알고리즘 문제를 풀 때에는 위 세 가지 검증을 다 하지는 않더라도, 주어진 테스트 케이스를 넣고 실행한 다음, 예상되는 결과와 비교하는 방식으로 수행할 수 있다.

만약 메인 코드가 너무 길고 복잡하면, 단위 테스트를 위해 파이참에서 새 창을 켜고 특정 메서드만 넣고, 필요한 입력값만 설정한 다음 테스트할 수 있다. 보다 정돈된 환경에서 검사를 할 수 있는 것이다.

<br>

메서드 하나하나에 대해 단위 테스트를 하는 것이 시간이 소요되는 것이기에 비효율적인 것이라 생각할 수 있지만, 실상은 그 반대이다.

가령 5개의 메서드를 만들고 나서, 마지막에 테스트 케이스를 넣고 출력 결과를 본다고 해보자. 원하는 결과가 나오지 않거나, 에러가 발생하면 어려운 디버깅을 해야 한다.

심지어 한 개의 메서드가 아닌, 두 개 이상의 메서드에 잘못된 부분이 있다면 더욱 오류를 찾는 것이 까다로워진다.

결과적으로 복합적인 오류에 대해 해결하는 데 오랜 시간이 걸리게 되고, 단위 테스트를 하면서 풀었을 때보다 시간이 더 소요될 가능성이 매우 높아지는 것이다.

반면 하나의 메서드에 대해 작성 도중, 그리고 완성 후에 단위 테스트를 한다면 훨씬 좁은 범위에서 오류를 파악할 수 있다.

그러므로 <b>메서드 하나 하나마다 단위 테스트를 하고, 출력 결과를 보면서 효율적으로 디버깅 한다.</b>

<br>

## 인덱스 처리는 입력을 받자마자 한다.

알고리즘 문제를 풀다 보면 인덱스 처리를 해야 하는 경우가 많다. 프로그래밍에서 인덱스의 시작은 0 인데, 문제에서 시작 인덱스를 1 로 설정하는 것이다.

입력값으로 2차원 배열을 받을 수도 있고, 리스트를 받을 수도 있다. 2차원 배열의 경우 (행, 열) 의 값이 (0, 0) 에서 시작해야 한다. 리스트의 경우 인덱스가 0 부터 시작해야 한다.

그런데 문제에서 2차원 배열을 그림으로 보여주며, 1행 1열을 (1, 1) 이라고 표현하는 경우가 있다. 리스트의 경우 첫 번째 값을 (1) 로 표현하는 경우가 있다.

이렇게 2차원 배열이나 1차원 리스트 외에도, 방향에 대한 처리를 할 때에 이러한 경우가 있다.

가령 상, 우, 하, 좌 방향 정보를 순서대로 받는다고 할 때, 각각 1, 2, 3, 4 의 값으로 설정한다고 하는 것이다. 만약 문제에서 시계방향 회전을 하면서 상황에 따라 방향을 바꾸는 작업을 해야 한다면, ```d = (d + 1) % 4``` 와 같은 방식으로 처리를 많이 한다. 그런데 1, 2, 3, 4 로 설정돼 있으면 이렇게 처리하기 쉽지 않다. 1, 2, 3, 4 각각에 대해 1씩 빼서 0, 1, 2, 3 으로 설정을 해놔야 문제 없이 적용할 수 있는 것이다.

<a href="https://www.acmicpc.net/problem/21611">백준 21611 마법사 상어와 블리자드</a> 문제가 정확히 이러한 상황이다. 문제에서 2차원 배열이 주어지는데 격자의 가장 왼쪽 윗 칸이 (1, 1) 이라고 하고, 4 가지 방향 ↑, ↓, ←, → 를 정수 1, 2, 3, 4 로 나타낸다고 한다.

문제를 읽고 이렇게 인덱스를 처리해서 접근해야 한다는 판단을 내렸다면, <u>입력값을 받자마자</u> -1 을 하는 등의 방식으로 처리를 해놓는 게 좋다.

만약 초반에 입력을 받자마자 인덱스 처리를 하지 않고, 받은 입력값을 사용할 때마다 -1 을 하는 방식으로 접근한다면 문제가 복잡해질 경우 놓칠 수 있다. 그리고 어떤 메서드에 인자로 넣거나, 메인에서 사용할 때마다 '인덱스를 처리해야 한다' 라는 생각을 한다면 너무 심리적인 에너지를 많이 소모하는 것이다.

입력값을 받자마자 인덱스 처리를 하는 코드 예시는 다음과 같다.

```python
lst = []
for _ in range(M):
    sr, sc, er, ec = map(int, input().split())
    sr -= 1
    sc -= 1
    er -= 1
    ec -= 1
    lst.append([sr, sc, er, ec])
```

위와 같이 바로 각 값들을 1 씩 뺀 다음 리스트로 묶어서 lst라는 리스트에 append 한 것이다.

그러므로 <b>인덱스를 처리해야 하는 문제의 경우 입력값을 받자마자 처리하여, 실수를 방지하고 해당 부분에 대해 불필요하게 에너지를 쏟지 않도록 한다.</b>

<br>

## 엣지 케이스 (edge case) 를 직접 만들어서 실행시킨다.

코딩 테스트 때에는 답안을 제출해도 결과를 바로 알 수 없다. 주어진 테스트 케이스에 대한 출력값이 정확히 나왔다 하더라도, 히든 테스트 케이스 (hidden test case)에 걸려서 오답 처리될 수도 있는 것이다.

그래서 코딩 테스트 때 내 답안에 대해 검증할 때, 주어진 테스트 케이스들에 대해 출력값이 잘 나왔다고 안심해서는 안 된다.

엣지 케이스(edge case)를 직접 생각해보고, 생성하고 코드에 넣어봐야 한다.

엣지 케이스란 프로그램이나 시스템이 처리할 때 예상치 못한 문제를 발생시킬 수 있는 특이한 입력이나 상황을 의미한다. 일반적인 상황에서는 잘 작동하지만, 극단적인 조건이나 비정상적인 상황에서 발생할 수 있는 예외적인 경우를 다룬다.

엣지 케이스의 예시는 다음과 같다.

- 배열의 첫 번째 또는 마지막 요소가 포함된 경우
- 입력값이 매우 크거나 작은 경우 (ex. 음수, 최대치 등)
- 빈 입력 값, null 값이 포함된 경우

엣지 케이스에 대한 고려를 하지 않으면 프로그램이 비정상적으로 동작할 수 있어, 이를 고려해 코딩하는 것이 중요하다.

엣지 케이스에 해당하는 테스트 케이스를 만들 때에는 다음과 같은 다양한 시나리오를 고려하는 것이 좋다.

#### 입력 크기

* <b>최소 입력값</b> - 입력값이 최소일 때를 고려해야 한다. 예를 들어, 빈 리스트, 빈 문자열, 혹은 숫자 0과 같은 입력이 이에 해당한다. Ex. ```[]```, ```""```, ```0```

* <b>최대 입력값</b> - 입력값이 최대일 때도 중요한 테스트 케이스이다. 특히 입력 크기가 크면 성능 문제나 메모리 초과가 발생할 수 있다. Ex. 최대 길이의 배열, 긴 문자열, 매우 큰 숫자 등.

#### 값의 경계

* <b>첫 번째 및 마지막 요소</b> - 리스트나 배열에서 첫 번째 혹은 마지막 요소를 다루는 코드가 제대로 작동하는지 확인해야 한다. Ex. ```array[0]```, ```array[-1]``` 에 대한 처리

* <b>경계 값</b> - 최대 또는 최소 값 바로 직전, 직후의 값에 대한 처리를 해야 한다. Ex. ```int``` 타입에서 ```INT_MAX```, ```INT_MIN``` 또는 그에 인접한 값.

#### 특이한 값

* <b>0 또는 음수 값</b> - 숫자 문제일 경우 0이나 음수는 엣지 케이스가 될 수 있다. Ex. ```0```, ```-1```, ```-100``` 과 같은 입력.

* <b>동일한 값이 여러 개</b> - 배열에 같은 값이 여러 개 있을 때, 중복된 값을 제대로 처리할 수 있는지. Ex. ```[1, 1, 1, 1]```, ```[5, 5, 5, 5]```

#### 특정 패턴

* <b>정렬된 데이터</b> - 입력이 오름차순이나 내림차순으로 정렬된 경우. Ex. ```[1, 2, 3, 4, 5]```, ```[5, 4, 3, 2, 1]```

* <b>역순으로 정렬된 데이터</b> - 예상과 반대로 정렬된 데이터도 고려. Ex. ```[9, 8, 7, 6, 5]```

#### 불가능하거나 예외적인 경우

* <b>불가능한 조건</b> - 예를 들어, 나눗셈에서 분모가 0이 되는 경우.

* <b>None, Null 값</b> - 예상치 못한 빈 입력이 있을 때, 프로그램이 제대로 처리하는지. Ex. ```None```, ```null```, ```undefined``` 값에 대한 처리.

#### 중복 요소나 특수한 구조

* <b>중복 요소</b> - 배열에 중복된 값이 포함되어 있는 경우. Ex. ```[1, 1, 2, 3, 4]```

* <b>특수한 구조</b> - 비대칭적인 트리나 그래프 구조 등도 엣지 케이스로 다룰 수 있다. Ex. 트리의 한쪽으로 치우친 구조

위와 같이 다양한 요소들을 고려해, 답안을 최종 제출하기 전에 엣지 케이스들을 입력값으로 넣어 프로그램의 완전성을 높여야 한다.

<br>

## 반례에 대한 고려를 한다.