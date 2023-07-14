---
layout: post
title: HashMap
description: Java의 정석 기초편 ch. 11 컬렉션 프레임웍
categories: Java
date: 2023-05-24 16:51:00 +09:00
---
Hashtable과 HashMap의 관계는 Vector와 ArrayList의 관계와 같아서 Hashtable보다는 새로운 버전인 HashMap을 사용하는 것이 권장된다.

HashMap은 Map을 구현했으므로 앞에서 살펴본 Map의 특징, 키(key)와 값(value)을 묶어서 하나의 데이터(entry)로 저장한다는 특징을 가진다. 그리고 해싱(hashing)을 사용하기 때문에 많은 양의 데이터를 검색하는 데 있어 뛰어난 성능을 보인다.

아래는 HashMap이 데이터를 어떻게 저장하는지 확인하기 위해 실제 소스의 일부를 발췌한 것이다.

```java
public class HashMap extends AbstractMap implements Map, Cloneable, Serializable {
  transient Entry[] table;
    ...
  static class Entry implements Map.Entry {
    final Object key;
    Object value;
      ...
  }
}
```

HashMap은 Entry라는 내부 클래스를 정의하고, 다시 Entry타입의 배열을 선언하고 있다. 키(key)와 값(value)은 별개의 값이 아니라 서로 관련된 값이기 때문에 각각의 배열로 선언하기보다는 하나의 클래스로 정의해서 배열로 다루는 것이 데이터의 무결성(integrity)적인 측면에서 더 바람직하기 때문이다. 

<table>
  <tr>
    <th>비객체지향적인 코드</th>
    <th>객체지향적인 코드</th>
  </tr>
  <tr>
    <td>
<pre>
<code>
Object[] key;
Object[] value;
</code>
</pre>
	</td>
    <td>
<pre>
<code>
Entry[] table;

class Entry {
	Object key;
	Object value;
}
</code>
</pre>
	</td>
  </tr>
</table>

(Map.Entry는 Map인터페이스에 정의된 'static inner interface'이다.)


## HashMap의 키(key)와 값(value)

HashMap은 키와 값을 각각 Object타입으로 저장한다. 즉 (Object, Object)의 형태로 저장하기 때문에 어떠한 객체도 저장할 수 있지만 키는 주로 String을 대문자 또는 소문자로 통일해서 사용하곤 한다.

> **키(key)** - 컬렉션 내의 키(key) 중에서 유일해야 한다.\
> **값(value)** - 키(key)와 달리 데이터의 중복을 허용한다.

키는 저장된 값을 찾는 데 사용되는 것이기 때문에 컬렉션 내에서 유일(unique)해야 한다. 즉, HashMap에 저장된 데이터를 하나의 키로 검색했을 때 결과가 단 하나이어야 함을 뜻한다. 만일 하나의 키에 대해 여러 검색결과 값을 얻는다면 값이 어떤 것인지 알 수 없기 때문이다. 

예를 들어 사용자ID가 키(key)로, 비밀번호가 값(value)으로 연결되어 저장된 데이터 집합이 있다고 가정하자. 로그인 시에 비밀번호를 확인하기 위해서 입력된 사용자ID에 대한 비밀번호를 검색했을 때, 단 하나의 결과를 얻어야만 올바른 비밀번호를 입력했는지 확인이 가능할 것이다. 만일 하나의 사용자ID에 대해서 두 개 이상의 비밀번호를 얻는다면 어떤 비밀번호가 맞는 것인지 알 수 없다. 


## HashMap 예제 1

```java
import java.util.*;

class Ex11_16 {
	public static void main(String[] args) {
		HashMap map = new HashMap();
		map.put("myId", "1234");
		map.put("asdf", "1111");
		map.put("asdf", "1234");

		Scanner s = new Scanner(System.in);	// 화면으로부터 라인단위로 입력받는다.

		while(true) {
			System.out.println("id와 password를 입력해주세요.");
			System.out.print("id :");
			String id = s.nextLine().trim();

			System.out.print("password :");
			String password = s.nextLine().trim();
			System.out.println();

			if(!map.containsKey(id)) {
				System.out.println("입력하신 id는 존재하지 않습니다. 다시 입력해주세요.");
				continue;
			} 
			
			if(!(map.get(id)).equals(password)) {
				System.out.println("비밀번호가 일치하지 않습니다. 다시 입력해주세요.");
			} else {
				System.out.println("id와 비밀번호가 일치합니다.");
				break;
			}
		} // while
	} // main의 끝
}
```

위 코드의 출력 결과 예시는 다음과 같다.

```
id와 password를 입력해주세요.
id :asdf
password :1111

비밀번호가 일치하지 않습니다. 다시 입력해주세요.
id와 password를 입력해주세요.
id :asdf
password :1234

id와 비밀번호가 일치합니다.
```

HashMap을 생성하고 사용자ID와 비밀번호를 키와 값의 쌍(pair)으로 저장한 다음, 입력된 사용자ID를 키로 HashMap에서 검색해서 얻은 값(비밀번호)을 입력된 비밀번호와 비교하는 예제이다.

```java
HashMap map = new HashMap();
map.put("myId", "1234");
map.put("asdf", "1111");
map.put("asdf", "1234");    // OK. 이미 존재하는 키 추가 가능. 기존 값은 없어짐
```

위의 코드는 HashMap을 생성하고 데이터를 저장하는 부분인데 이 코드가 실행되고 나면 HashMap에는 아래와 같은 형태로 데이터가 저장된다.

<table>
  <tr>
    <th>키(key)</th>
    <th>값(value)</th>
  </tr>
  <tr>
    <td>myId</td>
    <td>1234</td>
  </tr>
  <tr>
    <td>asdf</td>
    <td>1234</td>
  </tr>
</table>

3개의 데이터 쌍을 저장했지만 실제로는 2개밖에 저장되지 않은 이유는 중복된 키가 있기 때문이다. 세 번째로 저장한 데이터의 키인 'asdf'는 이미 존재하기 때문에 새로 추가되는 대신 기존의 값을 덮어썼다. 그래서 키 'asdf'에 연결된 값은 '1234'가 된다.

Map은 값은 중복을 허용하지만 키는 중복을 허용하지 않기 때문에 저장하려는 두 데이터 중에서 어느 쪽을 키로 할 것인지 잘 결정해야 한다.

(Hashtable은 키(key)나 값(value)으로 null을 허용하지 않지만, HashMap은 허용한다. 그래서 'map.put(null, null);'이나 'map.get(null);'과 같이 할 수 있다.)


## HashMap 예제 2

```java
import java.util.*;

class Ex11_17 {
	public static void main(String[] args) {
		HashMap map = new HashMap();
		map.put("김자바", 90);
		map.put("김자바", 100);
		map.put("이자바", 100);
		map.put("강자바", 80);
		map.put("안자바", 90);

		Set set = map.entrySet();
		Iterator it = set.iterator();

		while(it.hasNext()) {
			Map.Entry e = (Map.Entry)it.next();
			System.out.println("이름 : "+ e.getKey() + ", 점수 : " + e.getValue());
		}

		set = map.keySet();
		System.out.println("참가자 명단 : " + set);

		Collection values = map.values();
		it = values.iterator();

		int total = 0;
		
		while(it.hasNext()) {
			int i = (int)it.next();
			total += i;
		}

		System.out.println("총점 : " + total);
		System.out.println("평균 : " + (float)total/set.size());
		System.out.println("최고점수 : " + Collections.max(values));
		System.out.println("최저점수 : " + Collections.min(values));
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
이름 : 안자바, 점수 : 90
이름 : 김자바, 점수 : 100
이름 : 강자바, 점수 : 80
이름 : 이자바, 점수 : 100
참가자 명단 : [안자바, 김자바, 강자바, 이자바]
총점 : 370
평균 : 92.5
최고점수 : 100
최저점수 : 80
```


## HashMap 예제 3

```java
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;

class Ex11_18 {
	public static void main(String[] args) {
		String[] data = { "A","K","A","K","D","K","A","K","K","K","Z","D" };

		HashMap map = new HashMap();

		for(int i=0; i < data.length; i++) {
			if(map.containsKey(data[i])) {
				int value = (int)map.get(data[i]);
				map.put(data[i], value+1);  // 기존에 존재하는 키면 기존 값을 1증가
			} else {
				map.put(data[i], 1);	    // 기존에 존재하지 않는 키는 값을 1로 저장		
			}
		}

		Iterator it = map.entrySet().iterator();

		while(it.hasNext()) {
			Map.Entry entry = (Map.Entry)it.next();
			int value = (int)entry.getValue();
			System.out.println(entry.getKey() + " : " + printBar('#', value) + " " + value );
		}
	} // main

	public static String printBar(char ch, int value) { 
		char[] bar = new char[value]; 

		for(int i=0; i < bar.length; i++)
			bar[i] = ch; 

		return new String(bar); // String(char[] chArr)
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
A : ### 3
D : ## 2
Z : # 1
K : ###### 6
```

문자열 배열에 담긴 문자열을 하나씩 읽어서 HashMap에 키로 저장하고 값으로 1을 저장한다. HashMap에 같은 문자열이 키로 저장되어 있는지 containsKey()로 확인하여 이미 저장되어 있는 문자열이면 값을 1 증가시킨다.

그리고 그 결과를 printBar()를 이용해서 그래프로 표현했다. 이렇게 하면 문자열 배열에 담긴 문자열들의 빈도수를 구할 수 있다. 

한정된 범위 내에 있는 순차적인 값들의 빈도수는 배열을 이용하지만, 이처럼 한정되지 않은 범위의 비순차적인 값들의 빈도수는 HashMap을 이용해서 구할 수 있다.

(결과를 통해 HashMap과 같이 해싱을 구현한 컬렉션 클래스들은 저장 순서를 유지하지 않는다는 사실을 다시 한 번 확인할 수 있다.)
