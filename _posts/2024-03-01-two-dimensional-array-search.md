---
layout: post
title: 2차원 배열 탐색
description: 델타 배열을 활용한 2차원 배열 탐색 - 자바
categories: Algorithm
date: 2024-03-01 23:08:00 +0900
---
2차원 배열 탐색이란, 쭉 순회를 돌다가 특정 원소를 만나면 그 원소의 상하좌우, 필요하면 대각선까지 보는 것이다.

가령 행의 길이가 4, 열의 길이가 4인 2차원 배열이 있다고 해보자. 특정 원소를 발견했을 때, 그 좌표를 (1, 1)이라고 한다면 그 위의 좌표는 (0, 1), 아래는 (2, 1), 왼쪽은 (1, 0) 오른쪽은 (1, 2)가 된다. 이렇게 상, 하, 좌, 우를 탐색하는 것을 <b>4방 탐색</b>이라고 한다.

상, 하, 좌, 우 외에 대각선까지 고려한다면 <b>8방 탐색</b>을 사용하면 된다.

<br>

## 델타 배열 (delta)

좌표값의 차이만큼 배열을 미리 만들어서 사용하는 방법이다.

만약 4방 탐색을 위한 상, 하, 좌, 우 델타 배열은 다음과 같다.

```java
int[] dr = { -1, 1, 0, 0 };     // 상 하 좌 우
int[] dc = { 0, 0, -1, 1 };     // 상동
```

또, 8방 탐색을 위한 상, 하, 좌, 우, 좌상, 우상, 좌하, 우하 델타 배열은 다음과 같다.

```java
int[] dr = { -1, 1, 0, 0, -1, -1, 1, 1 };   // 상 하 좌 우 좌상 우상 좌하 우하
int[] dc = { 0, 0, -1, 1, -1, 1, -1, 1 };   // 상동
```

기준점이 있을 때, 위 델타 배열을 이용해서 쭉 돌면서 dr과 dc를 더해주면 순서대로 인접한 원소의 좌표가 나온다. 이렇게 델타 배열을 이용하면 탐색을 수월하게 할 수 있다.

<br>

그리고 델타 배열을 활용할 때에는 <b>경계 조건</b>을 고려해야 한다.

델타 배열을 활용해 사방 혹은 팔방 탐색을 할 때, 배열의 범위가 초과될 수 있기 때문이다.

<br>

경계 조건까지 고려한 예시 코드를 보겠다.

```java
package test07_탐색;

public class Array02_2차원배열_사방탐색_경계조건 {
    public static void main(String[] args) {
        
        // 다음 배열에서 1이 들어있는 칸의 상하좌우에 인접한 칸에
        // 2를 채워 넣으시오.
        
        int[][] arr = {
                {0, 0, 0, 1, 0},
                {0, 1, 0, 0, 0},
                {0, 0, 0, 0, 1},
                {1, 0, 0, 1, 0},
                {0, 0, 1, 0, 0}
        };
        
        int N = 5;
        
        // 델타배열 만들기
        int[] dr = {-1, 1, 0, 0}; // 상하좌우
        int[] dc = {0, 0, -1, 1};
        
        // 1. 행 우선 순회
        for(int r = 0; r < N; r++) {
        	for(int c = 0; c < N; c++) {
        		if(arr[r][c] == 1) { // 1인 원소를 발견했다면,
        			// (r, c) => 1인 원소의 좌표, 기준점!
        			System.out.println("r: " + r + ", c: " + c);
        			// (r, c)를 기준으로 해서, 상하좌우의 인접 좌표를 만든다.
        			
        			for(int d=0; d<4; d++) {
        				// 새로운 좌표
        				int nr = r + dr[d];
        				int nc = c + dc[d];
        				
        				// 문제점 => 경계조건을 고려하지 않았다!
        				// 행렬의 범위를 벗어나는 좌표가 만들어질 수도 있다.
        				// "새로운 좌표가 행렬의 index범위 안에 있다면.." => 경계조건
        				if(nr >= 0 && nr < N && nc >= 0 && nc <N)
        					arr[nr][nc] = 2;
        			}
        		}
        	}
        }
     
        for(int r = 0; r < N; r++) {
        	for(int c = 0; c < N; c++) {
        		System.out.printf("%2d", arr[r][c]);
        	}
        	System.out.println();
        }  
    }
}
```

위 코드의 실행 결과는 다음과 같다.

```
r: 0, c: 3
r: 1, c: 1
r: 2, c: 4
r: 3, c: 0
r: 3, c: 3
r: 4, c: 2
 0 2 2 1 2
 2 1 2 2 2
 2 2 0 2 1
 1 2 2 1 2
 2 2 1 2 0
```

배열에서 1이 들어있는 칸의 상하좌우에 인접한 칸에 2를 채워 넣는 문제였다.

경계조건을 고려하였다.

<br>

## 사방탐색 탐색 길이

다음으로, 사방탐색의 탐색 길이에 대해 보겠다. 인접한 원소 하나씩만 탐색하는 것이 아니라, 그 방향으로 지정한 개수만큼 탐색을 하게 하는 것이다.

가령, 주어진 배열에서 1을 만났을 때, 1의 사방에 길이가 2 만큼 숫자를 2로 바꾸고 싶은 것이다.

코드로 보면 다음과 같다.

```java
package test07_탐색;

public class Array03_2차원배열_사방탐색_탐색길이 {
    public static void main(String[] args) {
        
        // 다음 배열에서 1이 들어있는 칸의 상하좌우 방향으로 
        // 길이가 2인 막대를 이어 붙인다. (막대는 2를 채워넣어 표현한다.)
        
        int[][] arr = {
                {0, 0, 0, 0, 0, 0, 0},
                {0, 0, 0, 0, 0, 0, 0},
                {0, 0, 0, 0, 0, 0, 0},
                {0, 0, 0, 1, 0, 0, 0},
                {0, 0, 0, 0, 0, 0, 0},
                {0, 0, 0, 0, 0, 0, 0},
                {0, 0, 0, 0, 0, 0, 0}
        };
        
        int N = 7;
        
        // 델타배열 만들기
        int[] dr = {-1, 1, 0, 0}; // 상하좌우
        int[] dc = {0, 0, -1, 1};
        
        // 1. 행 우선 순회
        for(int r = 0; r < N; r++) {
        	for(int c = 0; c < N; c++) {
        		if(arr[r][c] == 1) { // 1인 원소를 발견했다면,
        			// (r, c) => 1인 원소의 좌표, 기준점!
        			System.out.println("r: "+r+", c: "+c);
        			// (r, c)를 기준으로 해서, 상하좌우의 인접 좌표를 만든다.
        			
        			for(int d = 0; d < 4; d++) {
        				// 새로운 좌표
        				// 길이도 고려해줘야 한다.
        				// => 그 길이만큼 반복.
        				for(int l = 1; l <= 2; l++) {
        					
        					int nr = r + l * dr[d];
            				int nc = c + l * dc[d];
            				
            				// 경계조건 
            				if(nr >= 0 && nr < N && nc >= 0 && nc <N)
            					arr[nr][nc] = 2;
        				}
        				
        			}
        		}
        	}
        }
     
        for(int r = 0; r < N; r++) {
        	for(int c = 0; c < N; c++) {
        		System.out.printf("%2d", arr[r][c]);
        	}
        	System.out.println();
        }
    }
}
```

위 코드의 실행 결과는 다음과 같다.

```
r: 3, c: 3
 0 0 0 0 0 0 0
 0 0 0 2 0 0 0
 0 0 0 2 0 0 0
 0 2 2 1 2 2 0
 0 0 0 2 0 0 0
 0 0 0 2 0 0 0
 0 0 0 0 0 0 0
```

인접한 방향으로 탐색을 할 때, 그 탐색을 이어나가도록 길이만큼 반복을 해줘야 한다.

새로운 좌표를 만들 때, 길이까지 고려하였다. 각 방향으로 길이를 l 이라 하고, 1부터 2까지 반복하도록 설정하였다. 그리고 ```l * dr[d];```와 같은 방식으로 기입하였다.

<br>

## 사방탐색 탐색 끝까지

이번에는 탐색을 끝까지 하는 것을 알아보겠다. 경계 조건을 벗어나지 않는 선에서, 끝까지 탐색을 하는 것이다.

탐색 길이가 주어졌다면, 반복 횟수를 알 수 있고, for 문을 사용하면 된다. 반면에, 탐색을 끝까지 해야 한다면, 반복 횟수가 어떻게 될지 모른다. 이런 경우에는 while문을 사용해야 한다.

코드를 보면 다음과 같다.

```java
package test07_탐색;

public class Array03_2차원배열_사방탐색_탐색끝까지 {
	public static void main(String[] args) {

		// 다음 배열에서 1이 들어있는 칸을 포함한 행과 열에
		// 모두 2를 채워 넣는다.(1이 들어있는 칸 제외)

		int[][] arr = { 
			{ 0, 0, 0, 0, 0, 0, 0 }, 
			{ 0, 0, 0, 0, 0, 0, 0 }, 
			{ 0, 0, 0, 0, 0, 0, 0 },
			{ 0, 0, 0, 0, 0, 0, 0 }, 
			{ 0, 0, 0, 0, 1, 0, 0 }, 
			{ 0, 0, 0, 0, 0, 0, 0 }, 
			{ 0, 0, 0, 0, 0, 0, 0 }
		};

		int N = 7;

		// 탐색 길이-> 반복 횟수 -> for문
		// 탐색 끝까지 -> 조건 (반복 횟수가 어떻게 될지 모른다.) -> while문

		// 델타배열 만들기
		int[] dr = { -1, 1, 0, 0 }; // 상하좌우
		int[] dc = { 0, 0, -1, 1 };

		// 1. 행 우선 순회
		for (int r = 0; r < N; r++) {
			for (int c = 0; c < N; c++) {
				if (arr[r][c] == 1) { // 1인 원소를 발견했다면,
					// (r, c) => 1인 원소의 좌표, 기준점!
					System.out.println("r: " + r + ", c: " + c);
					// (r, c)를 기준으로 해서, 상하좌우의 인접 좌표를 만든다.

					for (int d = 0; d < 4; d++) {
						// 끝까지 가는 경우
						// while문

						// 먼저 해당 방향으로 첫번째 인접원소를 만들고
						int nr = r + dr[d];
						int nc = c + dc[d];

						while (nr >= 0 && nr < N && nc >= 0 && nc < N) { // 경계를 벗어나지 않았다면
							arr[nr][nc] = 2; // 2를 채우고
							nr += dr[d]; // 그 방향으로 한번 더 간다.
							nc += dc[d];
						}
					}

				}
			}
		}

		for (int r = 0; r < N; r++) {
			for (int c = 0; c < N; c++) {
				System.out.printf("%2d", arr[r][c]);
			}
			System.out.println();
		}
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
r: 4, c:4
 0 0 0 0 2 0 0
 0 0 0 0 2 0 0
 0 0 0 0 2 0 0
 0 0 0 0 2 0 0
 2 2 2 2 1 2 2
 0 0 0 0 2 0 0
 0 0 0 0 2 0 0
```

탐색을 끝까지 하기 위해 while문을 사용하였다.

팔방탐색을 하고 싶다면 델타 배열을 새로 생성하고 마찬가지 방법으로 하면 되겠다.