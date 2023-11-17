---
layout: post
title: 모험가 길드 문제
description: 이것이 취업을 위한 코딩 테스트다 with 파이썬 p.311
categories: Algorithm
date: 2023-11-17 23:32:00 +0900
---
모험가 길드 문제의 경우, 처음에는 n개 만큼의 그룹이 있다고 전제하고, 반복문을 돌면서 공포도 리스트에서 n보다 큰 수가 있을 경우 n을 1씩 줄이는 방법으로 접근하려고 했다. 하지만 더 이상 진행이 안 됐다.

오랜 시간 고민을 하다가, 답안 설명을 보았다. 우선 공포도 리스트를 sort를 하고, 처음에 팀의 개수인 guild를 0으로 설정한 다음, count를 0으로 설정한다.

그렇게 해서 오름차순 정리된 공포도 리스트에서 하나씩 볼 때마다 count를 하나씩 늘린다. 그리고 count가 i 이상이 될 경우, 즉 여태까지의 count가 공포도가 i인 사람을 만났을 때 guild를 1 더하고 count는 0으로 초기화 한다. guild를 1 추가한 것은 길드의 수를 하나 확정한 것이다.

답안 코드는 다음과 같다.

```python
n = int(input())
fear_lst = list(map(int, input().split()))
fear_lst.sort()

guild = 0
count = 0

for i in fear_lst:
    count += 1
    if count >= i:
        guild += 1
        count = 0

print(guild)
```

<br>

예제 입력

```
5
2 3 1 2 2
```

예제 출력

```
2
```