---
layout: post
title: Java로 DFS, BFS 구현
categories: Algorithm
description: 백준 1260번 - DFS와 BFS
date: 2025-05-19 16:09:00 +0900
---
Java로 DFS, BFS 문제를 풀어봤다. <a href="https://www.acmicpc.net/problem/1260" target="_blank">백준 1260번 - DFS와 BFS</a> 문제이다.

```java
import java.io.*;
import java.util.*;

class Main {
	
	static List<List<Integer>> graph = new ArrayList<>();
	static boolean[] visited;   // 아직 메인에서 N 을 받기 전이기 때문에 선언만 해둔다.
	
	public static void main(String[] args) throws IOException {
		
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st;
		
		st = new StringTokenizer(br.readLine());
		int N = Integer.parseInt(st.nextToken());
		int M = Integer.parseInt(st.nextToken());
		int V = Integer.parseInt(st.nextToken());
		
		for (int i = 0; i < N + 1; i++) {
			graph.add(new ArrayList<>());
		}
		
		for (int i = 0; i < M; i++) {
			st = new StringTokenizer(br.readLine());
			int s = Integer.parseInt(st.nextToken());
			int e = Integer.parseInt(st.nextToken());
			
			graph.get(s).add(e);
			graph.get(e).add(s);
		}
		
        // 작은 노드 번호부터 들어가게 하기 위해 정렬
		for (List<Integer> g : graph) {
			Collections.sort(g);
		}
		
        // visited 배열 초기화
		visited = new boolean[N + 1];
		List<Integer> result = new ArrayList<>();
		
		dfs(V, result);
		
		StringBuilder sb = new StringBuilder();
		for (int res : result) {
			sb.append(res).append(" ");
		}
		System.out.println(sb.toString().trim());
		
        // visited 배열 초기화 (재사용 목적)
		visited = new boolean[N + 1];
		result = new ArrayList<>();     // 재사용 목적으로 초기화
		
		bfs(V, result);
		
		sb = new StringBuilder();
		for (int res : result) {
			sb.append(res).append(" ");
		}
		System.out.println(sb.toString().trim());		
	}
	
	private static void dfs(int current, List<Integer> result) {
		visited[current] = true;
		result.add(current);
		
		for (int next : graph.get(current)) {
			if (!visited[next]) {
				dfs(next, result);
			}
		}
	}
	
	private static void bfs(int start, List<Integer> result) {
		Deque<Integer> queue = new ArrayDeque<>();
		visited[start] = true;
		queue.offerLast(start);
		result.add(start);
		
		while (!queue.isEmpty()) {
			int current = queue.pollFirst();
			for (int next : graph.get(current)) {
				if (!visited[next]) {
					visited[next] = true;
					queue.offerLast(next);
					result.add(next);
				}
			}
		}
	}
}
```

```main()```과 ```dfs()``` 모두 static 메서드이기 때문에, 메서드 간 공통된 상태 공유를 위해 ```graph```, ```visited```도 ```static```으로 선언하였다.

물론, ```dfs()```의 인자로 넘기는 방식으로도 가능하지만, 대부분의 알고리즘 풀이에서는 간단하고 빠르게 구현하기 위해 ```static``` 전역 변수를 사용한다고 한다.