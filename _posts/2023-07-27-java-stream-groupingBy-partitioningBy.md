---
layout: post
title: 스트림의 그룹화와 분할
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-27 09:44:00 +0900
---
지금까지는 기존의 다른 연산으로도 대체 가능한 경우에 대해서 설명했기 때문에, collect()가 왜 필요한지 잘 느끼지 못했을 것이다. 그러나 이제부터는 본격적으로 collect()의 유용함을 알게 될 것이다.

그룹화는 스트림의 요소를 특정 기준으로 그룹화하는 것을 의미하고, 분할은 스트림의 요소를 두 가지, 지정된 조건에 일치하는 그룹과 일치하지 않는 그룹으로의 분할을 의미한다. 아래의 메서드 정의에서 알 수 있듯이, groupingBy()는 스트림의 요소를 Function으로, practitioningBy()는 Predicate로 분류한다.

```java
Collector partitioningBy(Predicate predicate)
Collector partitioningBy(Predicate predicate, Collector downstream)

Collector groupingBy(Function classifier)
Collector groupingBy(Function classifier, Collector downstream)
Collector groupingBy(Function classifier, Supplier mapFactory, Collector downstream)
```

메서드의 정의를 보면 groupingBy()와 partitioningBy()가 분류를 Function으로 하느냐 Predicate로 하느냐의 차이만 있을 뿐 동일하다는 것을 알 수 있다. 스트림을 두 개의 그룹으로 나눠야 한다면, 당연히 paritioningBy()로 분할하는 것이 더 빠르다. 그 외에는 groupingBy()를 쓰면 된다. 그리고 그룹화와 분할의 결과는 Map에 담겨 반환된다.

메서드의 정의만으로는 잘 감이 오지 않을 것이다. 이제 이 메서드들이 실제로 어떻게 쓰이는지 직접 보면 생각보다 어렵지 않다고 느끼게 될 것이다.


## 스트림의 분할 - partitioningBy()

먼저 상대적으로 간단한 partitioningBy()부터 시작하겠다. partitioningBy()를 이해하고 나면, groupingBy()는 쉽게 이해가 될 것이다. 가장 기본적인 분할은 학생들을 성별로 나누어 List에 담는 것이다.

```java
// 1. 기본 분할
Map<Boolean, List<Student>> stuBySex = stuStream.collect(partitioningBy(Student::isMale));      // 학생들을 성별로 분할

List<Student> maleStudent = stuBySex.get(true);     // Map에서 남학생 목록을 얻는다.
List<Student> femaleStudent = stuBySex.get(false);  // Map에서 여학생 목록을 얻는다.
```

이번에는 counting()을 추가해서 남학생의 수와 여학생의 수를 구해보겠다.

```java
// 2. 기본 분할 + 통계 정보
Map<Boolean, Long> stuNumBySex = stuStream.collect(partitioningBy(Student::isMale, counting()));

System.out.println("남학생 수 :" + stuNumBySex.get(true));      // 남학생 수 : 8
System.out.println("여학생 수 :" + stuNumBySex.get(false));     // 여학생 수 : 10
```

counting() 대신 summingLong()을 사용하면, 남학생과 여학생의 총점을 구할 수 있다. 그러면, 남학생 1등과 여학생 1등은 어떻게 구할 수 있을까?

```java
Map<Boolean, Optional<Student>> topScoreBySex = stuStream.collect(partitioningBy(Student::isMale, maxBy(comparingInt(Student::getScore))));

System.out.println("남학생 1등 :" + topScoreBySex.get(true));
System.out.println("여학생 1등 :" + topScoreBySex.get(false));
// 남학생 1등 :Optional[[나자바, 남, 1, 1, 300]]
// 여학생 1등 :Optional[[김지미, 여, 1, 1, 250]]
```

mapBy()는 반환타입이 Optional&#60;Student&#62;라서 위와 같은 결과가 나왔다. Optional&#60;Student&#62;가 아닌 Student를 반환 결과로 얻으려면, 아래와 같이 collectingAndThen()과 Optional::get을 함께 사용하면 된다.

```java
Map<Boolean, Student> topScoreBySex = stuStream.collect(partitioningBy(Student::isMale, collectingAndThen(maxBy(comparingInt(Student::getScore)), Optional::get)));
// 남학생 1등 :Optional[[나자바, 남, 1, 1, 300]]
// 여학생 1등 :Optional[[김지미, 여, 1, 1, 250]]
System.out.println("남학생 1등 :" + topScoreBySex.get(true));
System.out.println("여학생 1등 :" + topScoreBySex.get(false));
```

성적이 150점 아래인 학생들은 불합격처리하고 싶다. 불합격자를 성별로 분류하여 얻어내려면 어떻게 해야 할까? partitioningBy()를 한 번 더 사용해서 이중 분할을 하면 된다.

```java
Map<Boolean, Map<Boolean, List<Student>>> failedStuBySex = stuStream.collect(partitioningBy(Student::isMale, partitioningBy(s -> s.getScore() < 150)));
List<Student> failedMaleStu = failedStuBySex.get(true).get(true);
List<Student> failedFemaleStu = failedStuBySex.get(false).get(true);
```


## 스트림의 분할 - partitioningBy() 예제

```java
import java.util.*;
import java.util.function.*;
import java.util.stream.*;
import static java.util.stream.Collectors.*;
import static java.util.Comparator.*;

class Student2 {
	String name;
	boolean isMale; // 성별 
	int hak;        // 학년 
	int ban;        // 반 
	int score;

	Student2(String name, boolean isMale, int hak, int ban, int score) { 
		this.name	= name;
		this.isMale	= isMale;
		this.hak	= hak;
		this.ban	= ban;
		this.score  = score;
	}
	String	getName()     { return name; }
	boolean  isMale()     { return isMale; }
	int      getHak()     { return hak; }
	int      getBan()     { return ban; }
	int      getScore()   { return score; }

	public String toString() { 
		return String.format("[%s, %s, %d학년 %d반, %3d점]",
			name, isMale ? "남":"여", hak, ban, score); 
	}
   // groupingBy()에서 사용 
	enum Level { HIGH, MID, LOW }  // 성적을 상, 중, 하 세 단계로 분류 
}

class Ex14_10 {
	public static void main(String[] args) {
		Student2[] stuArr = {
			new Student2("나자바", true,  1, 1, 300),	
			new Student2("김지미", false, 1, 1, 250),	
			new Student2("김자바", true,  1, 1, 200),	
			new Student2("이지미", false, 1, 2, 150),	
			new Student2("남자바", true,  1, 2, 100),	
			new Student2("안지미", false, 1, 2,  50),	
			new Student2("황지미", false, 1, 3, 100),	
			new Student2("강지미", false, 1, 3, 150),	
			new Student2("이자바", true,  1, 3, 200),	
			new Student2("나자바", true,  2, 1, 300),	
			new Student2("김지미", false, 2, 1, 250),	
			new Student2("김자바", true,  2, 1, 200),	
			new Student2("이지미", false, 2, 2, 150),	
			new Student2("남자바", true,  2, 2, 100),	
			new Student2("안지미", false, 2, 2,  50),	
			new Student2("황지미", false, 2, 3, 100),	
			new Student2("강지미", false, 2, 3, 150),	
			new Student2("이자바", true,  2, 3, 200)	
		};

		System.out.printf("1. 단순분할(성별로 분할)%n");
		Map<Boolean, List<Student2>> stuBySex =  Stream.of(stuArr)
				.collect(partitioningBy(Student2::isMale));

		List<Student2> maleStudent   = stuBySex.get(true);
		List<Student2> femaleStudent = stuBySex.get(false);

		for(Student2 s : maleStudent)   System.out.println(s);
		for(Student2 s : femaleStudent) System.out.println(s);

		System.out.printf("%n2. 단순분할 + 통계(성별 학생수)%n");
		Map<Boolean, Long> stuNumBySex = Stream.of(stuArr)
				.collect(partitioningBy(Student2::isMale, counting()));

		System.out.println("남학생 수 :" + stuNumBySex.get(true));
		System.out.println("여학생 수 :" + stuNumBySex.get(false));

		System.out.printf("%n3. 단순분할 + 통계(성별 1등)%n");
		Map<Boolean, Optional<Student2>> topScoreBySex = Stream.of(stuArr)
				.collect(partitioningBy(Student2::isMale, 
					maxBy(comparingInt(Student2::getScore))
				));
		System.out.println("남학생 1등 :" + topScoreBySex.get(true));
		System.out.println("여학생 1등 :" + topScoreBySex.get(false));

		Map<Boolean, Student2> topScoreBySex2 = Stream.of(stuArr)
			.collect(partitioningBy(Student2::isMale, 
				collectingAndThen(
					maxBy(comparingInt(Student2::getScore)), Optional::get
				)
			));

		System.out.println("남학생 1등 :" + topScoreBySex2.get(true));
		System.out.println("여학생 1등 :" + topScoreBySex2.get(false));
		
		System.out.printf("%n4. 다중분할(성별 불합격자, 100점 이하)%n");

		Map<Boolean, Map<Boolean, List<Student2>>> failedStuBySex = 
			Stream.of(stuArr).collect(partitioningBy(Student2::isMale, 
				partitioningBy(s -> s.getScore() <= 100))
			); 
		List<Student2> failedMaleStu   = failedStuBySex.get(true).get(true);
		List<Student2> failedFemaleStu = failedStuBySex.get(false).get(true);

		for(Student2 s : failedMaleStu)   System.out.println(s);
		for(Student2 s : failedFemaleStu) System.out.println(s);
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
1. 단순분할(성별로 분할)
[나자바, 남, 1학년 1반, 300점]
[김자바, 남, 1학년 1반, 200점]
[남자바, 남, 1학년 2반, 100점]
[이자바, 남, 1학년 3반, 200점]
[나자바, 남, 2학년 1반, 300점]
[김자바, 남, 2학년 1반, 200점]
[남자바, 남, 2학년 2반, 100점]
[이자바, 남, 2학년 3반, 200점]
[김지미, 여, 1학년 1반, 250점]
[이지미, 여, 1학년 2반, 150점]
[안지미, 여, 1학년 2반,  50점]
[황지미, 여, 1학년 3반, 100점]
[강지미, 여, 1학년 3반, 150점]
[김지미, 여, 2학년 1반, 250점]
[이지미, 여, 2학년 2반, 150점]
[안지미, 여, 2학년 2반,  50점]
[황지미, 여, 2학년 3반, 100점]
[강지미, 여, 2학년 3반, 150점]

2. 단순분할 + 통계(성별 학생수)
남학생 수 :8
여학생 수 :10

3. 단순분할 + 통계(성별 1등)
남학생 1등 :Optional[[나자바, 남, 1학년 1반, 300점]]
여학생 1등 :Optional[[김지미, 여, 1학년 1반, 250점]]
남학생 1등 :[나자바, 남, 1학년 1반, 300점]
여학생 1등 :[김지미, 여, 1학년 1반, 250점]

4. 다중분할(성별 불합격자, 100점 이하)
[남자바, 남, 1학년 2반, 100점]
[남자바, 남, 2학년 2반, 100점]
[안지미, 여, 1학년 2반,  50점]
[황지미, 여, 1학년 3반, 100점]
[안지미, 여, 2학년 2반,  50점]
[황지미, 여, 2학년 3반, 100점]
```


## 스트림의 그룹화 - groupingBy()

일단 가장 간단한 그룹화를 해보자. stuStream을 반 별로 그룹지어 Map에 저장하는 방법은 다음과 같다.

```java
Map<Integer, List<Student>> stuByBan = stuStream.collect(groupingBy(Student::getBan));      // toList()가 생략됨
```

groupingBy()로 그룹화를 하면 기본적으로 List&#60;T&#62;에 담는다. 그래서 아래 문장을 위와 같이 쓸 수 있는 것이다. 만일 원한다면, toList() 대신 toSet()이나 toCollection(HashSet::new)을 사용할 수도 있다. 단, Map의 제네릭 타입도 적절히 변경해줘야 한다는 것을 잊지 말아야 한다.

```java
Map<Integer, List<Student>> stuByBan = stuStream.collect(groupingBy(Student::getBan, toList()));    // toList() 생략 가능

Map<Integer, HashSet<Student>> stuByHak = stuStream.collect(groupingBy(Student::getHak, toCollection(HashSet::new)));
```

이번에는 조금 복잡하게 stuStream을 성적의 등급(Student.Level)으로 그룹화 해보자. 아래의 문장은 모든 학생을 세 등급(HIGH, MID, LOW)으로 분류하여 집계한다.

```java
Map<Student.Level, Long> stuByLevel = stuStream.collect(groupingBy(s -> {
    if (s.getScore() >= 200)
        return Student.Level.HIGH;
    else if (s.getScore() >= 100)
        return Student.Level.MID;
    else
        return Student.Level.LOW;
}, counting()));
// [MID] - 8명, [HIGH] - 8명, [LOW] - 2명
```

groupingBy()를 여러 번 사용하면, 다수준 그룹화가 가능하다. 만일 학년별로 그룹화 한 후에 다시 반별로 그룹화하고 싶으면 다음과 같이 한다.

```java
Map<Integer, Map<Integer, List<Student>>> stuByHakAndBan = stuStream.collect(groupingBy(Student::getHak,    // 학년별 그룹화
                                                                            groupingBy(Student::getBan)     // 반별 그룹화
));
```

위의 코드를 발전시켜서 각 반의 1등을 출력하고 싶다면, collectingAndThen()과 max By()를 써서 다음과 같이 하면 된다.

```java
Map<Integer, Map<Integer, Student>> topStuByHakAndBan = stuStream
        .collect(groupingBy(Student::getHaK,
                groupingBy(Student::getBan,
                    collectingAndThen(
                        maxBy(comparingInt(Student::getScore)),
                        Optional::get
                    )
                )
        ));
```

아래의 코드는 학년별과 반별로 그룹화한 다음에, 성적 그룹으로 변환(mapping)하여 Set에 저장한다.

```java
Map<Integer, Map<Integer, Set<Student.Level>>> stuByHakAndBan = stuStream
    .collect(
        groupingBy(Student::getHak,
            groupingBy(Student::getBan,
                mapping(s -> {
                    if (s.getScore() >= 200)
                        return Student.Level.HIGH;
                    else if (s.getScore() >= 100)
                        return Student.Level.MID;
                    else
                        return Student.Level.LOW;
                }, toSet())
            )
        )
    );
```

## 스트림의 그룹화 - groupingBy() 예제

```java
import java.util.*;
import java.util.function.*;
import java.util.stream.*;
import static java.util.stream.Collectors.*;
import static java.util.Comparator.*;

class Student3 {
	String name;
	boolean isMale; // 성별 
	int hak;        // 학년 
	int ban;        // 반 
	int score;

	Student3(String name, boolean isMale, int hak, int ban, int score) { 
		this.name	= name;
		this.isMale	= isMale;
		this.hak   	= hak;
		this.ban	= ban;
		this.score 	= score;
	}

	String	getName() 	 { return name;    }
	boolean isMale()  	 { return isMale;  }
	int		getHak()   	 { return hak;	   }
	int		getBan()  	 { return ban;	   }
	int		getScore()	 { return score;   }

	public String toString() {
		return String.format("[%s, %s, %d학년 %d반, %3d점]", name, isMale ? "남" : "여", hak, ban, score);
	}

	enum Level {
		HIGH, MID, LOW
	}
}

class Ex14_11 {
	public static void main(String[] args) {
		Student3[] stuArr = {
			new Student3("나자바", true,  1, 1, 300),	
			new Student3("김지미", false, 1, 1, 250),	
			new Student3("김자바", true,  1, 1, 200),	
			new Student3("이지미", false, 1, 2, 150),	
			new Student3("남자바", true,  1, 2, 100),	
			new Student3("안지미", false, 1, 2,  50),	
			new Student3("황지미", false, 1, 3, 100),	
			new Student3("강지미", false, 1, 3, 150),	
			new Student3("이자바", true,  1, 3, 200),	
			new Student3("나자바", true,  2, 1, 300),	
			new Student3("김지미", false, 2, 1, 250),	
			new Student3("김자바", true,  2, 1, 200),	
			new Student3("이지미", false, 2, 2, 150),	
			new Student3("남자바", true,  2, 2, 100),	
			new Student3("안지미", false, 2, 2,  50),	
			new Student3("황지미", false, 2, 3, 100),	
			new Student3("강지미", false, 2, 3, 150),	
			new Student3("이자바", true,  2, 3, 200)	
		};

		System.out.printf("1. 단순그룹화(반별로 그룹화)%n");
		Map<Integer, List<Student3>> stuByBan = Stream.of(stuArr)
				.collect(groupingBy(Student3::getBan));

		for(List<Student3> ban : stuByBan.values()) {
			for(Student3 s : ban) {
				System.out.println(s);
			}
		}

		System.out.printf("%n2. 단순그룹화(성적별로 그룹화)%n");
		Map<Student3.Level, List<Student3>> stuByLevel = Stream.of(stuArr)
				.collect(groupingBy(s-> {
						 if(s.getScore() >= 200) return Student3.Level.HIGH;
					else if(s.getScore() >= 100) return Student3.Level.MID;
					else                         return Student3.Level.LOW;
				}));

		TreeSet<Student3.Level> keySet = new TreeSet<>(stuByLevel.keySet());

		for(Student3.Level key : keySet) {
			System.out.println("["+key+"]");

			for(Student3 s : stuByLevel.get(key))
				System.out.println(s);
			System.out.println();
		}

		System.out.printf("%n3. 단순그룹화 + 통계(성적별 학생수)%n");
		Map<Student3.Level, Long> stuCntByLevel = Stream.of(stuArr)
				.collect(groupingBy(s-> {
					     if(s.getScore() >= 200) return Student3.Level.HIGH;
					else if(s.getScore() >= 100) return Student3.Level.MID;
					else                         return Student3.Level.LOW;
				}, counting()));
		for(Student3.Level key : stuCntByLevel.keySet())
			System.out.printf("[%s] - %d명, ", key, stuCntByLevel.get(key));
		System.out.println();
/*
		for(List<Student3> level : stuByLevel.values()) {
			System.out.println();
			for(Student3 s : level) {
				System.out.println(s);
			}
		}
*/
		System.out.printf("%n4. 다중그룹화(학년별, 반별)");
		Map<Integer, Map<Integer, List<Student3>>> stuByHakAndBan =
          Stream.of(stuArr)
				.collect(groupingBy(Student3::getHak,
						 groupingBy(Student3::getBan)
				));

		for(Map<Integer, List<Student3>> hak : stuByHakAndBan.values()) {
			for(List<Student3> ban : hak.values()) {
				System.out.println();
				for(Student3 s : ban)
					System.out.println(s);
			}
		}

		System.out.printf("%n5. 다중그룹화 + 통계(학년별, 반별 1등)%n");
		Map<Integer, Map<Integer, Student3>> topStuByHakAndBan =
          Stream.of(stuArr)
				.collect(groupingBy(Student3::getHak,
						 groupingBy(Student3::getBan,
						     collectingAndThen(
						         maxBy(comparingInt(Student3::getScore))
						         , Optional::get
						     )
						 )
				));

		for(Map<Integer, Student3> ban : topStuByHakAndBan.values())
			for(Student3 s : ban.values())
				System.out.println(s);

		System.out.printf("%n6. 다중그룹화 + 통계(학년별, 반별 성적그룹)%n");
		Map<String, Set<Student3.Level>> stuByScoreGroup = Stream.of(stuArr)
			.collect(groupingBy(s-> s.getHak() + "-" + s.getBan(),
					mapping(s-> {
						 if(s.getScore() >= 200) return Student3.Level.HIGH;
					else if(s.getScore() >= 100) return Student3.Level.MID;
						 else                    return Student3.Level.LOW;
					} , toSet())
			));

		Set<String> keySet2 = stuByScoreGroup.keySet();

		for(String key : keySet2) {
			System.out.println("["+key+"]" + stuByScoreGroup.get(key));
		}
	}  // main의 끝 
}
```

위 코드의 출력 결과는 다음과 같다.

```
1. 단순그룹화(반별로 그룹화)
[나자바, 남, 1학년 1반, 300점]
[김지미, 여, 1학년 1반, 250점]
[김자바, 남, 1학년 1반, 200점]
[나자바, 남, 2학년 1반, 300점]
[김지미, 여, 2학년 1반, 250점]
[김자바, 남, 2학년 1반, 200점]
[이지미, 여, 1학년 2반, 150점]
[남자바, 남, 1학년 2반, 100점]
[안지미, 여, 1학년 2반,  50점]
[이지미, 여, 2학년 2반, 150점]
[남자바, 남, 2학년 2반, 100점]
[안지미, 여, 2학년 2반,  50점]
[황지미, 여, 1학년 3반, 100점]
[강지미, 여, 1학년 3반, 150점]
[이자바, 남, 1학년 3반, 200점]
[황지미, 여, 2학년 3반, 100점]
[강지미, 여, 2학년 3반, 150점]
[이자바, 남, 2학년 3반, 200점]

2. 단순그룹화(성적별로 그룹화)
[HIGH]
[나자바, 남, 1학년 1반, 300점]
[김지미, 여, 1학년 1반, 250점]
[김자바, 남, 1학년 1반, 200점]
[이자바, 남, 1학년 3반, 200점]
[나자바, 남, 2학년 1반, 300점]
[김지미, 여, 2학년 1반, 250점]
[김자바, 남, 2학년 1반, 200점]
[이자바, 남, 2학년 3반, 200점]

[MID]
[이지미, 여, 1학년 2반, 150점]
[남자바, 남, 1학년 2반, 100점]
[황지미, 여, 1학년 3반, 100점]
[강지미, 여, 1학년 3반, 150점]
[이지미, 여, 2학년 2반, 150점]
[남자바, 남, 2학년 2반, 100점]
[황지미, 여, 2학년 3반, 100점]
[강지미, 여, 2학년 3반, 150점]

[LOW]
[안지미, 여, 1학년 2반,  50점]
[안지미, 여, 2학년 2반,  50점]


3. 단순그룹화 + 통계(성적별 학생수)
[HIGH] - 8명, [LOW] - 2명, [MID] - 8명, 

4. 다중그룹화(학년별, 반별)
[나자바, 남, 1학년 1반, 300점]
[김지미, 여, 1학년 1반, 250점]
[김자바, 남, 1학년 1반, 200점]

[이지미, 여, 1학년 2반, 150점]
[남자바, 남, 1학년 2반, 100점]
[안지미, 여, 1학년 2반,  50점]

[황지미, 여, 1학년 3반, 100점]
[강지미, 여, 1학년 3반, 150점]
[이자바, 남, 1학년 3반, 200점]

[나자바, 남, 2학년 1반, 300점]
[김지미, 여, 2학년 1반, 250점]
[김자바, 남, 2학년 1반, 200점]

[이지미, 여, 2학년 2반, 150점]
[남자바, 남, 2학년 2반, 100점]
[안지미, 여, 2학년 2반,  50점]

[황지미, 여, 2학년 3반, 100점]
[강지미, 여, 2학년 3반, 150점]
[이자바, 남, 2학년 3반, 200점]

5. 다중그룹화 + 통계(학년별, 반별 1등)
[나자바, 남, 1학년 1반, 300점]
[이지미, 여, 1학년 2반, 150점]
[이자바, 남, 1학년 3반, 200점]
[나자바, 남, 2학년 1반, 300점]
[이지미, 여, 2학년 2반, 150점]
[이자바, 남, 2학년 3반, 200점]

6. 다중그룹화 + 통계(학년별, 반별 성적그룹)
[1-1][HIGH]
[2-1][HIGH]
[1-2][LOW, MID]
[2-2][LOW, MID]
[1-3][HIGH, MID]
[2-3][HIGH, MID]
```