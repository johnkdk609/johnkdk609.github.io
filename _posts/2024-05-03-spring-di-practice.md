---
layout: post
title: Spring DI 연습
categories: Spring-SpringBoot
date: 2024-05-03 18:31:00 +0900
---
의존성을 주입하기 위해서는 XML, Annotation, JavaConfig의 방법들을 사용할 수 있다.

## Spring DI - XML

우선 XML을 사용하는 방법을 이어서 알아보자.

의존성을 주입하기 위해서 applicationContext.xml을 활용했었다. 이번에는 Test2를 해보기 위해 applicationContext2.xml을 만들어보겠다.

resources 폴더에 applicationContext2.xml을 생성하고 기존 applicationContext2.xml의 코드를 복사 붙여넣기 한다. applicationContext2.xml 의 코드는 다음과 같다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

		<!-- 풀패키지명 -->
		<bean class="com.ssafy.di.Desktop" id="desktop"></bean>
<!-- 	<bean class="com.ssafy.di.Programmer" id="programmer">
			<constructor-arg ref="desktop"></constructor-arg>
		</bean> -->
		<bean class="com.ssafy.di.Programmer" id="programmer">
			<!-- setter의 이름 -->
			<property name="computer" ref="desktop"></property>
		</bean>
</beans>
```

그리고 이제 Test2를 보자. Test2의 코드는 다음과 같다.

```java
package com.ssafy.di;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

public class Test2 {
	public static void main(String[] args) {

		ApplicationContext context = new GenericXmlApplicationContext("applicationContext2.xml");

		Programmer p = context.getBean("programmer", Programmer.class);
		
		p.coding();
		
	}
}
```

위 코드의 실행 결과는 다음과 같다.

<img src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7bc45831-8fae-42e6-ba99-0052ff15b4e8" width="300px" />

"데스크톱으로 개발을 합니다."가 알아서 튀어나온다. 조립을 applicationContext2.xml에서 한 것이다. 위에서 constructor-arg 방식으로 할 수도 있고, 또 property로 할 수도 있다. (property로 한다면 설정자를 넣어야 한다.) 그리고 참고할 ref는 "Desktop"으로 하였다.

<br>

정리하자면 다음과 같다.

### 의존성 주입 (생성자)

constructor-arg를 이용하여 의존성 주입. &#60;ref&#62;, &#60;value&#62; 와 같이 하위 태그를 이용하여 설정하거나, 속성을 이용하여 설정한다.

사용 예시는 다음과 같다.

```xml
<bean class="com.ssafy.di.Desktop" id="desktop"></bean>

<bean class="com.ssafy.di.Programmer" id="programmer">
	<constructor-arg ref="desktop"></constructor-arg>
</bean>
```

```java
package com.ssafy.di;

public class Programmer {
	private Computer computer;

    // 기본 생성자
	public Programmer() {
	}

	// 생성자를 이용한 의존성 주입
	public Programmer(Computer computer) {
		this.computer = computer;
	}

}
```

<br>

### 의존성 주입 (설정자)

setter를 이용하여 의존성 주입. &#60;ref&#62;, &#60;value&#62; 와 같이 하위 태그를 이용하여 설정하거나, 속성을 이용하여 설정할 수 있다.

```xml
<bean class="com.ssafy.di.Desktop" id="desktop"></bean>

<bean class="com.ssafy.di.Programmer" id="programmer">
    <!-- setter의 이름 -->
    <property name="computer" ref="desktop"></property>
</bean>
```

```java
package com.ssafy.di;

public class Programmer {
	private Computer computer;

    // 기본 생성자
	public Programmer() {
	}

	// 설정자(setter)을 이용한 의존성 주입
	public void setComputer(Computer computer) {
		this.computer = computer;
	}

}
```

위 XML에서 ref에는 내가 정의한 참조할 수 있는 값을 쓸 수 있다. 아니면 value로 해서 직접적으로 값을 쓸 수도 있다.

<br>

## Spring DI - Annotation

다음으로는 어노테이션 방식으로 주입을 해보자. 어노테이션(Annotation) 방식은 빈(Bean)을 생성 및 설정하는 데에 ```@Component```를 쓴다. 

```java
import org.springframework.stereotype.Component;

@Component
public class Desktop implements Computer {
	// 필요한 필드...
```

앞서 사용하던 예시인 Desktop 클래스 위에 ```@Component```를 넣으면 이전에 XML에서 bean~~ 해서 풀 패키지명을 쓰고 id를 지정했던 것이 알아서 되는 것이다.

생성되는 bean의 이름은 클래스의 첫 글자를 소문자로 바꾼 것이다. 가령, Desktop클래스는 "desktop"으로 빈이 생성된다. 만약에 빈의 이름을 지정하고 싶다면, ```@Component(value="bean-name")```으로 이름을 지정할 수 있다.

스프링은 <b>@Component</b>, <b>@Service</b>, <b>@Controller</b>, <b>@Repository</b> 의 Stereotype Annotation을 제공한다. (@Service, @Controller, @Repository는 결국 @Component 이다.)

각 @Repository, @Service, @Controller는 목적에 맞는 구체적인 사용을 위한 @Component의 확장이다. 목적에 맞게 구체화 하여 사용하면 Spring에서 더 효율적으로 사용하는 것이 가능해진다.

<br>

Annotation 방식을 쓴다 하더라도 XML 파일은 있어야 한다. @Component 를 붙인 것을 전부 스캔해서 알아서 빈으로 등록하게 해야 한다.

이제 자바 프로젝트 Spring_01_DI_3_Annotation 을 생성한다. Spring_01_DI_2_XML 의 내용을 복사 붙여넣기 하고, pom.xml 파일에서 groupId를 변경한다. (나머지는 손댈 것이 없다.)

"내가 컴포넌트로 등록한 것을 몽땅 스캔해줘"라는 것이니, resources 폴더 안에 있는 applicationContext.xml 에서 ```<context:component-scan base-package="com.ssafy.di"></context:component-scan>```를 입력한다. 이것은 com.ssafy.di 패키지에 있는 모든 것들을 전부 스캔을 하면서 ```@Component```라는 것이 붙어 있는 것은 모조리 등록을 하게 하는 것이다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
	
	<context:component-scan base-package="com.ssafy.di"></context:component-scan>
	
</beans>
```

내가 Programmer 클래스에 위에 ```@Component```을 붙이면 programmer라는 빈이 등록된다. 이때 빈의 이름을 따로 지정하기 위해 ```@Component(value="p")```를 붙였다. 

```java
@Component(value="p")
public class Programmer {
	...
```

그리고 Desktop 클래스에도 ```@Component```를 붙인다.

이제 Test 클래스에 다음과 같이 입력한다.

```java
package com.ssafy.di;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

public class Test {
	public static void main(String[] args) {

		ApplicationContext context = new GenericXmlApplicationContext("applicationContext.xml");

		Programmer p = context.getBean("p", Programmer.class);
		p.coding();
			
	}
}
```

아까 "p"라는 이름으로 빈을 등록했으니, 위와 같이 ```context.getBean("p", Programmer.class);```을 하였다. 

그런데 실행을 시키면 무언가 에러가 발생한다.

<img width="451" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/e29cd97e-13e9-4155-93a6-8747189b66c7">

이렇게 "데스크탑이 생성되었습니다."까지는 잘 나오는데, 다음과 같이 NullPointerException이 발생하는 것이다.

<img width="1147" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/568882b8-5120-41de-8dd9-79c02fc91a47">

Programmer 클래스의 coding()에 있는 ```computer.getInfo()```를 하려는데, ```computer```가 null이라서 현재 ```.getInfo()```를 할 수 없는 상황인 것이다. 그런데 데스크탑은 생성되었다. 현재 컴퓨터는 만들어졌고, 인간도 만들었는데 이 두 개가 조립이 안 된 것이다.

이 <b>조립을 하는 방법이 @Autowired 를 붙이는 것이다.</b> @Autowired 는 의존성을 주입할 때 붙이는 것으로, 이 어노테이션을 붙이면 알아서 조립을 할 수 있도록 세팅을 해준다.

Spring Container 내에서 타입 매칭을 시행한다. (컨테이너에 해당하는 타입의 bean이 있다면 매칭)

<br>

@Autowired 가 들어갈 수 있는 위치는 세 가지가 있다.

* <b>생성자</b>

```java
// 생성자를 이용한 의존성 주입
// 생성자를 하나만 정의한다면 @Autowired 생략 가능
@Autowired
public Programmer(Computer computer) {
	this.computer = computer;
}
```

* <b>Setter</b>

```java
// setter을 이용한 의존성 주입
@Autowired
public void setComputer(@Qualifier("desktop") Computer computer) {
	this.computer = computer;
}
```

* <b>Field</b>

```java
@Autowired
@Qualifier("laptop")
private Computer computer;
```

(@Qualifier 를 이용하여 같은 타입이 여러 개일 경우, bean을 지정하여 식별 가능하다.)

<br>

### 생성자 주입

우선 생성자 주입부터 해보겠다. Programmer 클래스의 생성자에다가 ```@Autowired```를 붙였다. 

```java
package com.ssafy.di;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component(value="p")
public class Programmer {

	private Computer computer;

	public Programmer() {
	}

	// 생성자 주입
	@Autowired
	public Programmer(Computer computer) {
		this.computer = computer;
	}

	public void setComputer(Computer computer) {
		this.computer = computer;
	}

	public void coding() {
		System.out.println(computer.getInfo() + "으로 개발을 합니다.");
	}
}

```

그리고 다시 Test 클래스를 실행시키니 다음과 같이 정상적으로 작동하였다.

<img width="456" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/6f2538f7-4fa9-4cef-90d0-91c397aa1eca">

현재 Programmer는 Computer을 처리할 수 있게끔 만들어놨는데, Desktop을 만들어놨더니 Computer와 알아서 매칭이 되었고, 이것을 쓸 수 있게 된 것이다.

그런데 앞서 생성자를 하나만 정의하면 @Autowired를 생략할 수 있다고 하였다. 이 말인즉 Programmer 클래스에서 @Autowired를 지우고, 기본 생성자를 지우면 현재 가지고 있는 생성자는 한 개이다. 이 한 개가 마치 내가 주입하는 생성자인 것처럼 작동하는 것이다. 이제 Test를 실행하면 정상적으로 잘 동작한다.

생성자가 한 개였으면 거기에 알아서 @Autowired가 붙는 것과 똑같은 것이다. (하지만 권장하지는 않는다. 한 개든 두 개든 @Autowired를 작성하는 것이 권장된다.)

<br>

### 설정자 주입

이제 설정자 주입을 해보자.

```java
package com.ssafy.di;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component(value="p")
public class Programmer {
//	@Autowired
//	private final Computer computer;
	private Computer computer;

	public Programmer() {
	}

	// 생성자 주입
//	@Autowired
	public Programmer(Computer computer) {
		this.computer = computer;
	}

	// 설정자 주입
	@Autowired
	public void setComputer(Computer computer) {
		this.computer = computer;
	}

	public void coding() {
		System.out.println(computer.getInfo() + "으로 개발을 합니다.");
	}
}
```

위와 같이 설정자 위에 @Autowired를 붙이고 Test를 실행하면 여전히 잘 동작한다. setter를 통해서 주입이 된 것이다.

<br>

### 필드 주입

마지막 방법은 필드에 주입하는 방법이다. 다음과 같이 할 수 있다.

```java
package com.ssafy.di;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component(value="p")
public class Programmer {
	// 필드 주입
	@Autowired
	private Computer computer;

	public Programmer() {
	}

	public void coding() {
		System.out.println(computer.getInfo() + "으로 개발을 합니다.");
	}
}
```

위와 같이 필드 주입을 하고 Test 클래스를 실행시키면 마찬가지로 잘 동작한다.

그런데 이렇게 필드에 주입하는 것은 웬만하면 사용하는 것을 지양하는 것이 좋다. 쓰기는 아주 쉬우나, 사용하는 것을 지양하는 것이다.

<br>

생성자 주입을 가장 크게 권장하는데, 그 이유는 생성을 할 때부터 바로 집어넣어서 처리를 해야 하는 것들에 주로 생성자 주입을 사용한다. 설정자 주입은 나중에 설정자를 불렀을 때 주입이 되는 것이니 애초에 만들었을 때도 이슈가 없는 것들을 집어넣으려 할 때 설정자 주입을 사용한다.

그런데 이 예시에서 Computer는 무조건 있어야 한다. 생성자 주입이 그래서 이 상황에서는 바람직한 것이다. 이런 상황을 방지하기 위해 ```private final Computer computer```와 같이 final 키워드를 붙여버리기도 한다. 

```java
package com.ssafy.di;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component(value="p")
public class Programmer {
	@Autowired
	private final Computer computer;

//	public Programmer() {
//	}

	// 생성자 주입
	@Autowired
	public Programmer(Computer computer) {
		this.computer = computer;
	}

	// 설정자 주입 
//	@Autowired
//	public void setComputer(Computer computer) {
//		this.computer = computer;
//	}

	public void coding() {
		System.out.println(computer.getInfo() + "으로 개발을 합니다.");
	}
}
```

final 키워드가 붙는 순간 기본 생성자는 만들 수 없다. final이 붙는다는 것은 computer이 무조건 필요하다는 뜻인데, 기본 생성자는 이렇게 만들 수가 없다. 

마찬가지로 이렇게 final을 붙이면 설정자 주입을 쓸 수도 없다. 무조건 있어야 하기 때문이다.

<br>

### 타입으로 찾아오기

이번에는 타입으로 찾아오는 것을 알아보겠다. Computer에 Desktop도 등록되어 있고, Laptop도 등록되어 있다. 이 중에 무엇으로 찾아올지 선택하는 것이다.

우선 Laptop 클래스도 @Component 로 등록을 한다. Laptop 클래스를 @Component로 등록하면 컴파일 시에는 아무런 문제가 없다. 그런데 실행을 시키면 에러가 터진다.

<img width="1708" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/7d7926f3-869a-452c-9fae-1ab01c0f2ac8">

위와 같이 single matching이 되어야 하는데, 현재 desktop과 laptop 두 개가 있다는 것이다.

이에 대한 해결책으로 Laptop 클래스를 @Component로 등록하지 않는 방법이 있다. 하지만, 또 다른 방법으로는, Programmer 클래스의 설정자 주입에서 @Qualifier를 사용하는 것이다. 다음과 같이 할 수 있다.

```java
package com.ssafy.di;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component(value="p")
public class Programmer {
//	@Autowired
//	private final Computer computer;
	private Computer computer;

	public Programmer() {
	}

	// 생성자 주입
//	@Autowired
	public Programmer(Computer computer) {
		this.computer = computer;
	}

	// 설정자 주입
//	@Autowired
//	public void setComputer(Computer computer) {
//		this.computer = computer;
//	}
	
	// 설정자 주입 & @Qualifier 사용
	@Autowired
	public void setComputer(@Qualifier("laptop") Computer computer) {
		this.computer = computer;
	}

	public void coding() {
		System.out.println(computer.getInfo() + "으로 개발을 합니다.");
	}
}
```

@Qulifier 어노테이션을 이용해, "laptop"으로 등록하겠다고 명시한 것이다.

이제 Test 클래스를 실행 시키면 다음과 같이 출력된다.

<img width="497" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/03dca01a-248a-42a5-b7f7-da2a0be07e3a">

이렇게 @Qualifier을 이용하여 같은 타입이 여러개일 경우 bean을 지정하여 식별할 수 있는 것이다.

<br>

## Spring DI - JavaConfig

이제 마지막 방식인 JavaConfig 방식을 알아보겠다.

이제 자바 프로젝트 Spring_01_DI_4_JavaConfig 를 생성한다. (Spring_01_DI_3_Annotation 프로젝트를 복사 붙여넣기 하고 이름을 변경한다.) pom.xml에 가서 groupId, artifactId를 변경한다. 자바 설정 파일에는 더 이상 XML이 필요하지 않다. 그래서 resources라는 폴더를 지운다. 그 다음, 일단 클래스들에 있는 @Component 어노테이션도 전부 지운다. 또 @Autowired, @Qualifier 어노테이션들도 전부 지운다.

이제 com.ssafy.di 패키지에 ApplicationConfig 라는 이름으로 클래스 파일을 하나 만든다. 그리고 @Configuration 어노테이션을 붙인다. 이제부터 이 클래스가 설정 파일이라는 것을 명시하는 것이다. ApplicationConfig 클래스의 코드는 다음과 같다.

```java
package com.ssafy.di;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ApplicationConfig {
	
	@Bean
	public Desktop desktop() {
		return new Desktop();
	}
	
	@Bean
	public Laptop laptop() {
		return new Laptop();
	}
	
	@Bean
	public Programmer programmer() {
		//생성자 주입 , 설정자 주입도 문제없어
		Programmer p = new Programmer(desktop());
		return p;
	}
}
```

위 코드에서, Desktop은 등록한 빈이고 빈의 이름은 desktop 이다. 마찬가지로 Laptop 빈의 이름은 laptop 이다. 그리고 이제 ```Programmer(desktop())```의 방식으로 사용할 수 있다. 여기서 생성자 주입을 하든, 설정자 주입을 하든 상관없다. 지금은 생성자 주입이지만, 설정자 주입도 문제 없는 것이다.

Test 클래스를 보자. 이전에는 다음과 같이 GenericXmlApplicationContext를 사용했었다.

```java
ApplicationContext context = new GenericXmlApplicationContext("applicationContext.xml");
```

그런데 이제는 AnnotationConfigApplicationContext를 사용해야 한다. Test 클래스의 코드는 다음과 같다.

```java
package com.ssafy.di;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Test {
	public static void main(String[] args) {

		ApplicationContext context = new AnnotationConfigApplicationContext(ApplicationConfig.class);
		
		Programmer p = context.getBean("programmer", Programmer.class);
		p.coding();
			
		Desktop d1 = context.getBean("desktop", Desktop.class);
		Desktop d2 = context.getBean("desktop", Desktop.class);
		
		System.out.println(d1==d2);
	
	}
}
```

이름이 굉장히 길다. 위 코드를 실행한 결과는 다음과 같다.

<img width="453" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/076488d7-35b4-45df-bdc6-e67f0fa42150">

잘 출력된다. 또, d1과 d2가 동일하다는 것은 싱글턴(Singleton)으로 관리가 되고 있다는 것을 알 수 있다.

<br>

이제 com.ssafy.di를 복사 붙여넣기 하고 이름을 com.ssafy.di2로 변경한다.

이번에는 ApplicationConfig 클래스에 전과 같이 전부 등록하지 않고, ComponentScan을 할 것이다.

수정한 ApplicationConfig 클래스의 코드는 다음과 같다.

```java
package com.ssafy.di2;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = {"com.ssafy.di2"} )
public class ApplicationConfig {
	
}
```

basePackages를 com.ssafy.di2로 하여, 알아서 @Component가 있는 클래스들을 읽고 빈을 등록하는 것이다. 이것을 수행하려면 com.ssafy.di2 패키지 안에 있는 Desktop, Programmer 클래스들에 @Component 어노테이션을 붙여놓는다.

Test 클래스의 코드는 다음과 같다.

```java
package com.ssafy.di2;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Test {
	public static void main(String[] args) {

		ApplicationContext context = new AnnotationConfigApplicationContext(ApplicationConfig.class);
		
		Programmer p = context.getBean("programmer", Programmer.class);
		p.coding();

	}
}
```

그리고 위 코드의 실행 결과는 다음과 같다.

<img width="449" alt="image" src="https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/cf08417e-dc31-4b1b-b61d-5adf39f43c2b">