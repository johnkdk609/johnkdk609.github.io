---
layout: post
title: 백트래킹 (Backtracking) 기본
categories: Algorithm
date: 2024-08-06 22:57:00 +0900
---
백트래킹(Backtracking)은 완전탐색 중의 하나이다.

<b>가능한 모든 경우를 처리</b>하는 알고리즘인 것이다. 다만 가지치기(pruning)라는 '탐색하다가 더 이상 가망이 없으면 잘라내는' 작업을 통해 탐색 범위를 줄인다는 점에서 완전탐색과는 차이가 있다.

이러한 가지치기를 잘 하지 못하면 설령 탐색은 성공적으로 이뤄지더라도, 시간 초과로 오답 처리가 될 수 있다. (백트래킹 문제를 풀 때에는 시간 초과 말고는 다른 이유로 오답이 되어서는 절대 안 된다.)

백트래킹을 표현할 때에는 트리(tree)를 사용하는 것이 가장 직관적이다.

<br>

다음과 같은 '주사위 던지기' 문제를 풀어보자. 문제는 다음과 같다.

Q. 5 이하의 자연수 N을 입력 받아 주사위를 N번 던져 나올 수 있는 모든 경우를 출력하는 프로그램을 작성하시오. (사전순) (1 &#60;&#61; N &#60;&#61; 5)

<br>

이에 대한 답안 코드는 다음과 같다. 이 답안 코드를 백트래킹의 템플릿(template)으로 생각해도 되겠다.

```python
def dfs(n, alst):
    if n == N:              # 종료 조건 : n 관련
        print(*alst)        # 정답 처리는 이곳에서
        return
    for j in range(1, 7):   # 하부 호출 : 가능한 경우
        dfs(n + 1, alst + [j])

T = int(input())
for tc in range(1, T + 1):
    N = int(input())

    print(f'#{tc}')
    dfs(0, [])
```

<br>

위 문제를 풀 때 고려해야 하는 것들은 순서대로 다음과 같다.

* <b>종료 조건과 관련된 "n"을 정의하는 것이 가장 중요하다.</b>

이때 n은 주사위를 던진 횟수로, 종료 조건(기저 조건)과 연관되게 잡아주는 것이 매우 중요하다. 이것을 어떻게 정하느냐에 따라 시간 초과가 발생할 수도 있고 아닐 수도 있기 때문이다.

* <b>트리(tree) 그리기</b>

선택할 수 있는 경우를 트리 상에 표시한다. 전체적인 표현에 대해 일반화를 잘 해야 한다. 위 문제의 경우 종료 조건은 N - 1 번째로 주사위를 던지고, N 번째로 주사위를 던지는 때이다. 모든 정답 처리는 이 '휴전선' (경계선)에서 하는 것이다. 중간에 정답 처리를 하면 시간을 줄일 수는 있겠지만 미처 생각하지 못한 반례, 혹은 선입견 때문에 잘못될 수 있다.

* <b>핵심 코드 손코딩</b>

백트래킹을 풀 때에는 중요한 것은 '한 시점을 dfs로 짜는 것'이다. 왜 dfs이고 왜 종료 조건을 고려해야 하는 것일까? 트리는 끝이 있다. 별도로 종료 조건을 명시하지 않아도, 리프 노드(leaf node)까지 가면 더 이상 갈 곳이 없는 것이다. 하지만 백트래킹에서의 탐색은 종료 조건을 명확히 하지 않으면 무한히 뻗어나간다.
백트래킹을 하면서 pruning을 하여 시간을 줄여야 한다. 그런데 무조건 가지치기를 많이 하면 할수록 시간이 줄 것이라 생각할 수도 있는데, 더 늘어날 수도 있다. 가지치기가 끝난 다음에는 시간 초과 여부를 확인하고 효율성 체크를 해야 한다.

* <b>가지치기</b>

마지막 단계에서 하는 것이다. 가지치기는 코드의 맨 윗부분에서 하는 것이고, 가장 마지막 단계(시간상)에서 작성하는 것이다. 더 이상 진행해도 의미 없는, 즉 정답에 영향을 미치지 않는 것들을 제하는 것이다.