---
layout: post
title: Programmers - 아이템 줍기
categories: Algorithm
description: Java에서의 해시 기반 컬렉션 비교 & 변형 BFS
date: 2025-10-12 00:30:00 +0900
---
<a href="https://school.programmers.co.kr/learn/courses/30/lessons/87694" target="_blank">Programmers - 아이템 줍기</a> 문제는 BFS 문제인데, 그 자체로도 흥미롭지만, Java 클래스로 풀 경우 해시 기반 컬렉션 비교를 위한 ```Override```를 수행해야 한다는 특징이 있다.

<br>
<hr>

## Java에서의 해시 기반 컬렉션 비교

2차원 격자 BFS 문제를 풀 때에는 수차례 queue에 ```(row, col)``` 값을 넣어야 한다. Python으로 할 경우, 간단하게 튜플(tuple)을 사용할 수 있다. Java로 풀 때에는 튜플이 아닌, 1차원 배열을 생성해서 그 안에 행, 열을 담는 ```queue.offerLast(new int[]{row, col});``` 과 같은 방식을 사용해야 한다. 그런데 '좌표'라는 점에서 본인은 ```Point```라는 클래스를 생성해서, ```queue.offerLast(new Point(row, col))```의 방식으로 접근하는 것을 선호한다. 클래스를 사용할 경우, 코드가 복잡해질수록 가독성이 증가하고 디버깅이 용이해진다는 측면이 있기 때문이다.

일반적으로는 ```Point``` 클래스를 생성하는 정도에서 그친다. 하지만 '아이템 줍기' 문제의 경우 Set을 사용해서 보다 효율적으로 풀려다 보니, ```Point``` 클래스들을 비교하는 로직이 필요했다.

새롭게 생성한 ```Point``` 클래스와 기존의 ```set``` 안에 있는 ```Point``` 클래스를 비교할 때, Java의 Set의 함수인 ```.contains()``` 를 사용하려면 추가적인 조치가 필요하다. 왜냐하면 새로 생성한 객체와 기존에 있던 객체의 지역 변수가 같더라도, 두 객체의 참조값이 다르기 때문이다.

예시를 보면 다음과 같다.

```java
Point a = new Point(1, 2);
Point b = new Point(1, 2);

System.out.println(a == b);         // false  → 주소 다름
System.out.println(a.equals(b));    // false  → equals()도 기본은 == 비교
```

즉, ```a```와 ```b```가 같은 좌표를 가지더라도 서로 다른 객체이기 때문에 기본 상태에서는 ```Set```이나 ```Map```이 "같은 값"으로 인식하지 못한다.

<br>

그래서 오버라이드(Override)가 필요하다.

```HashSet```・```HashMap``` 같은 해시 기반 컬렉션은 두 단계를 거쳐서 동등성 비교를 수행한다.

1. ```hashCode()``` - 객체가 들어갈 "버킷(bucket)"을 결정
2. ```equals()``` - 같은 버킷 내에서 실제 값이 같은지 비교

이 두 개가 짝을 맞춰서 동작해야 한다. 하나라도 빠지면 ```Set.contains(new Point(1, 2))``` 같은 게 제대로 작동하지 않는다.

<br>

그래서 답안 코드에서 Point 클래스를 다음과 같이 작성했다.

```java
private static class Point {
    int row;
    int col;
    Point(int row, int col) {
        this.row = row;
        this.col = col;
    }
    
    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (!(o instanceof Point)) {
            return false;
        }
        Point p = (Point) o;
        return row == p.row && col == p.col;
    }
    
    @Override
    public int hashCode() {
        return 31 * row + col;
    }
}
```

정리하자면, ```equals()``` 메서드는 두 객체의 "논리적 동등성"을 판단하는데, 오버라이드 한 이유는 좌표값이 같으면 같은 객체로 취급하기 위함이다. 그리고 ```hashCode()``` 메서드는 해시 버킷 위치 계산을 위한 것으로, 오버라이드 한 이유는 같은 좌표라면 같은 해시값으로 저장되도록 하기 위함이다.

<br>

그런데 궁금한 점이 생길 수 있다. <b>왜 ```hashCode()``` 함수에서 상수 31을 사용한 것일까?</b>

해시코드를 만들 때 보통 다음과 같이 한다.

```java
hash = a * prime + b;
```

이것은 "이전 값(hash)"을 적당한 소수(prime)로 확산시키면서 다음 필드를 더하는 방식이다.

이런 식으로 하면,

* ```a=1, b=2``` → ```hash=33```
* ```a=2, b=1``` → ```hash=63```

즉, (1, 2)와 (2, 1)의 해시값이 달라져서 <u>충돌을 줄이고 분포가 균등해진다.</u>

<br>

자바 설계자들이 31을 택한 이유는 두 가지이다.

1. <b>소수이기 때문</b>
    - 31은 홀수이면서 소수(prime)라서 ```31 * x + y``` 조합이 해시 충돌을 덜 일으킨다.
    - 짝수면 2로 나누는 연산에서 패턴이 겹치고 충돌이 늘어난다.
    - 소수는 수학적으로 해시 분포를 고르게 해준다.

2. <b>컴퓨터가 빠르게 계산할 수 있기 때문</b>
    - 31은 2⁵ - 1, 즉 32 - 1 이다.
    - 그래서 ```31 * x```는 다음과 같이 최적화 된다.
    ```java
    31 * x == (x << 5) - x      // (왼쪽으로 5비트 shift 후 x 빼기)
    ```
    - 곱셈보다 훨씬 빠른 비트 연산으로 처리 가능.
    - 실제로 자바 컴파일러는 이 최적화를 자동으로 수행함.

결국 31은 <u>충돌 적고, 계산 빠르고, 구현이 단순한</u> 이상적인 해시 상수로 선택된 것이다.

<br>
<hr>

## 변형 BFS

이제 문제에 집중해보자.

직사각형의 좌표가 주어져 있기에, 처음에는 입력으로 주어지는 ```rectangle```의 직사각형들이 포함하는 (행, 열) 좌표들을 전부 집합(Set)에 담으려 했다. 그런 다음, 집합 안에 있는 좌표들 모두 순회하면서 8방 탐색을 통해 단 하나라도 한 칸 진전한 좌표가 집합에 없으면 새로운 집합에 담았다.

이렇게 하면 결과적으로 '테두리'의 값들만 담겨 있는 집합을 구할 수 있게 되고, 그 집합에 있는 좌표들로 격자를 1로 채우고, 방문 체크 배열을 사용해서 효율적으로 문제를 해결하는 것이 initial thought 이었다.

<br>

그런데 이렇게 할 경우 엣지 케이스(edge case)가 발생했다.

예를 들어, 다음과 같이 두 직사각형이 위치해 있는 경우가 있겠다.

<img src="/assets/img/captures/programmers_item_picking_example.png" width="450px" />

위 예시를 보면, 두 직사각형이 딱 1의 길이로 서로 면이 접하고 있다. 이렇게 되면, BFS를 할 때 '테두리를 따라 이동'하는 점에서 문제가 발생할 수 있다. 내 로직에서는 일단 테두리 값들을 따로 추출한 다음, 해당 좌표값들을 격자(grid)에 담아두고 거기서 BFS를 하는 것이었는데, 위와 같은 케이스에서는 테두리를 따라 이동하는 게 아닌, '가로지르는' 문제가 발생할 수 있는 것이다.

즉 직사각형이 코너에서 맞닿는 경우 착시가 발생하는 것이다.

<br>

이에 대한 해결책은 <b>"좌표를 2배 확장해서 경계를 명확히 분리"</b>하는 것이다.

좌표를 2배 확장하면, 코너 사이에 빈 공간을 확보할 수 있다. 그리고 최단거리는 계산 후 ```/ 2```로 환산하면 된다.

예시를 간단히 들자면 다음과 같다.

```
원래 좌표: [1, 1, 7, 4]
확장 후: [2, 2, 14, 8]
```

<br>

답안 코드는 다음과 같다.

```java
import java.util.*;

class Solution {
    private static final int MAX = 51 * 2;  // 두 직사각형의 면이 한 칸만 겹치는 경우 발생하는 오류 방지하기 위해 두 배 확장
    
    private static int[][] grid = new int[MAX][MAX];
    private static boolean[][] visited = new boolean[MAX][MAX];
    private static int cx, cy, ix, iy;
    private static int[] dr = {-1, 0, 1, 0, -1, 1, 1, -1};  // 상 우 하 좌 상우 우하 하좌 좌상
    private static int[] dc = {0, 1, 0, -1, 1, 1, -1, -1};
    private static class Point {
        int row;
        int col;
        Point(int row, int col) {
            this.row = row;
            this.col = col;
        }
        
        @Override
        public boolean equals(Object o) {
            if (this == o) {
                return true;
            }
            if (!(o instanceof Point)) {
                return false;
            }
            Point p = (Point) o;
            return row == p.row && col == p.col;
        }
        
        @Override
        public int hashCode() {
            return 31 * row + col;
        }
    }
    private static class Result {
        int result;
        Result(int result) {
            this.result = result / 2;
        }
    }
    
    public int solution(int[][] rectangle, int characterX, int characterY, int itemX, int itemY) {
        int answer = 0;
        
        // 마찬가지로 전부 두 배로 확장 - 마지막에 이동 거리의 절반이 답이 될 것이다.
        cx = characterX * 2;
        cy = characterY * 2;
        ix = itemX * 2;
        iy = itemY * 2;
        
        // 집합을 만들어서 그 안에 모든 좌표들 넣는다.
        Set<Point> set = new HashSet<>();
        for (int[] rec : rectangle) {
            for (int x = rec[0] * 2; x <= rec[2] * 2; x++) {
                for (int y = rec[1] * 2; y <= rec[3] * 2; y++) {
                    set.add(new Point(x, y));
                }
            }
        }
        
        // 이제 집합 안에 있는 좌표들 전부 8방 탐색하며 하나라도 비는 곳이 있으면 finSet 에 넣는다.
        Set<Point> finSet = new HashSet<>();
        for (Point p : set) {
            for (int d = 0; d < 8; d++) {
                int nr = p.row + dr[d];
                int nc = p.col + dc[d];
                Point np = new Point(nr, nc);
                if (!set.contains(np)) {
                    finSet.add(p);
                    break;
                }
            }
        }
        
        // finSet 안에 있는 것들로 grid를 채운다.
        for (Point p : finSet) {
            grid[p.row][p.col] = 1;
        }
        
        // bfs를 실행하고, 나온 값의 절반을 answer에 담는다.
        Result res = bfs();
        answer = res.result;
        
        return answer;
    }
    
    private static Result bfs() {
        Deque<Point> queue = new ArrayDeque<>();
        queue.offerLast(new Point(cx, cy));
        visited[cx][cy] = true;
        
        int step = 0;
        
        while (!queue.isEmpty()) {
            int qSize = queue.size();
            for (int i = 0; i < qSize; i++) {
                Point current = queue.pollFirst();
                for (int d = 0; d < 4; d++) {
                    int nr = current.row + dr[d];
                    int nc = current.col + dc[d];
                    
                    if (nr == ix && nc == iy) {
                        return new Result(step + 1);
                    }
                    
                    if (!visited[nr][nc] && grid[nr][nc] == 1) {
                        visited[nr][nc] = true;
                        queue.offerLast(new Point(nr, nc));
                    }
                }
            }
            
            step++;
        }
        
        return new Result(-1);
    }
}
```