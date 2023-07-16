---
layout: post
title: 컬렉션 프레임워크와 함수형 인터페이스
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-16 14:42:00 +0900
---
컬렉션 프레임워크의 인터페이스에 다수의 디폴트 메서드가 추가되었는데, 그 중의 일부는 함수형 인터페이스를 사용한다. 다음은 그 메서드들의 목록이다.

(단순화하기 위해 와일드카드는 생략하였다.)

<table>
    <tr>
        <th>인터페이스</th>
        <th>메서드</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>Collection</td>
        <td>boolean removeIf(Predicate&#60;E&#62; filter)</td>
        <td>조건에 맞는 요소를 삭제</td>
    </tr>
    <tr>
        <td>List</td>
        <td>void replaceAll(UnaryOperator&#60;E&#62; operator)</td>
        <td>모든 요소를 변환하여 대체</td>
    </tr>
    <tr>
        <td>Iterable</td>
        <td>void forEach(Consumer&#60;T&#62; action)</td>
        <td>모든 요소에 작업 action을 수행</td>
    </tr>
    <tr>
        <td rowspan="6">Map</td>
        <td>V compute(K key, BiFunction&#60;K, V, V&#62; f)</td>
        <td>지정된 키의 값에 작업 f를 수행</td>
    </tr>
    <tr>
        <td>V computeIfAbsent(K key, Function&#60;K, V&#62; f)</td>
        <td>키가 없으면, 작업 f 수행 후 추가</td>
    </tr>
    <tr>
        <td>V computeIfPresent(K key, BiFunction&#60;K, V, V&#62; f)</td>
        <td>지정된 키가 있을 때, 작업 f 수행</td>
    </tr>
    <tr>
        <td>V merge(K key, V value, BiFunction&#60;V, V, V&#62; f)</td>
        <td>모든 요소에 병합작업 f를 수행</td>
    </tr>
    <tr>
        <td>void forEach(BiConsumer&#60;K, V&#62; action)</td>
        <td>모든 요소에 작업 action을 수행</td>
    </tr>
    <tr>
        <td>void replaceAll(BiFunction&#60;K, V, V&#62; f)</td>
        <td>모든 요소에 치환작업 f를 수행</td>
    </tr>
</table>

이름만 봐도 어떤 일을 하는 메서드인지 충분히 알 수 있을 것이다. Map인터페이스에 있는 'compute'로 시작하는 메서드들은 맵의 value를 변환하는 일을 하고 merge()는 Map을 병합하는 일을 한다. 이 메서드들을 어떤 식으로 사용하는 지는 다음의 예제를 보자.


## 컬렉션 프레임워크와 함수형 인터페이스 예제

```java
import java.util.*;

class Ex14_4 {
	public static void main(String[] args) 	{
		ArrayList<Integer> list = new ArrayList<>();
		for(int i=0;i<10;i++)
			list.add(i);

		// list의 모든 요소를 출력 
		list.forEach(i->System.out.print(i+","));
		System.out.println();

		// list에서 2 또는 3의 배수를 제거한다. 
		list.removeIf(x-> x%2==0 || x%3==0);
		System.out.println(list);

		list.replaceAll(i->i*10); // list의 각 요소에 10을 곱한다. 
		System.out.println(list);

		Map<String, String> map = new HashMap<>();
		map.put("1", "1");
		map.put("2", "2");
		map.put("3", "3");
		map.put("4", "4");

		// map의 모든 요소를 {k, v}의 형식으로 출력한다. 
		map.forEach((k,v)-> System.out.print("{"+k+","+v+"},"));
		System.out.println();
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
0,1,2,3,4,5,6,7,8,9,
[1, 5, 7]
[10, 50, 70]
{1,1},{2,2},{3,3},{4,4},
```

메서드의 기본적인 사용법만 보여주는 간단한 예제이므로 쉽게 이해가 될 것이다. 이 예제의 람다식을 변형해서 다양하게 테스트해보면 좋은 연습이 될 것이다.