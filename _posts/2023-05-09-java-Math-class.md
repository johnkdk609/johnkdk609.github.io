---
layout: post
title: Math클래스
categories: Java
description: Java의 정석 기초편 ch. 9 java.lang패키지와 유용한 클래스
date: 2023-05-09 13:45:00 +09:00
---
Math클래스는 기본적인 수학 계산에 유용한 메서드로 구성되어 있다. 임의의 수를 얻을 수 있는 random()과 반올림에 사용되는 round()는 이미 학습한 바 있다.

Math클래스의 생성자는 접근 제어자가 private이기 때문에 다른 클래스에서 Math인스턴스를 생성할 수 없도록 되어 있다. 그 이유는 클래스 내에 인스턴스변수가 하나도 없어서 인스턴스를 생성할 필요가 없기 때문이다. Math클래스의 메서드는 모두 static이며, 아래와 같이 2개의 상수만 정의해 놓고 있다. 

```java
public static final double E = 2.7182818284590452354;     // 자연로그의 밑
public static final double PI = 3.14159265358979323846;   // 원주율
```

### 올림, 버림, 반올림

소수점 n번째 자리에서 반올림한 값을 얻기 위해서는 round()를 사용해야 하는데, 이 메서드는 항상 소수점 첫째자리에서 반올림을 해서 정수값(long)을 결과로 돌려준다.

만약 내가 원하는 자리 수에서 반올림된 값을 얻기 위해서는 간단히 10의 n제곱으로 곱한 후, 다시 곱한 수로 나눠주기만 하면 된다. 예를 들어 90.7552라는 값을 소수점 셋째자리에서 반올림한 후 소수점 두 자리까지의 값만을 얻고 싶으면 90.7552에 100을 곱한다. 그 결과는 9075.52가 되며, 여기에 round()를 사용하게 되면 그 결과는 9076이 된다. 이 결과를 다시 100.0으로 나누면 90.76이라는 값을 얻게 된다. 

> 1. 원래 값에 100을 곱한다.
> > **90.7552 * 100 -> 9075.52**
> 2. 위의 결과에 Math.round()를 사용한다.
> > **Math.round(9075.52) -> 9076**
> 3. 위의 결과를 다시 100.0으로 나눈다.
> > **9076 / 100.0 -> 90.76**\
> > **9076 / 100 -> 90**

만일 정수형 값인 100 또는 100L로 나눈다면, 결과는 정수형 값을 얻게 될 것이다. 위의 경우 100.0 대신 100으로 나눈다면, 90이라는 정수값을 결과로 얻게 된다. **정수형 간의 연산에서는 반올림이 이루어지지 않는다는 것을 반드시 기억해야 한다.**

소수점 넷째자리에서 반올림된 소수점 세 자리 값을 얻으려면 100 대신 1000으로 곱하고 1000.0으로 나누면 된다. 그리고 반올림이 필요하지 않다면 round()를 사용하지 않고 단순히 1000으로 곱하고 1000.0으로 나누기만 하면 된다.


## Math의 메서드 예제

```java
import static java.lang.Math.*;
import static java.lang.System.*;

class Ex9_13 {
	public static void main(String args[]) {
		double val = 90.7552;
		out.println("round("+ val +")="+round(val));  // 반올림 

		val *= 100;
		out.println("round("+ val +")="+round(val));  // 반올림 

		out.println("round("+ val +")/100  =" + round(val)/100);   // 반올림 
		out.println("round("+ val +")/100.0=" + round(val)/100.0); // 반올림 
		out.println();
		out.printf("ceil(%3.1f)=%3.1f%n",  1.1, ceil(1.1));    // 올림 
		out.printf("floor(%3.1f)=%3.1f%n", 1.5, floor(1.5));   // 버림 
		out.printf("round(%3.1f)=%d%n",    1.1, round(1.1));   // 반올림 
		out.printf("round(%3.1f)=%d%n",    1.5, round(1.5));   // 반올림 
		out.printf("rint(%3.1f)=%f%n",     1.5, rint(1.5));    // 반올림 
		out.printf("round(%3.1f)=%d%n",   -1.5, round(-1.5));  // 반올림 
		out.printf("rint(%3.1f)=%f%n",    -1.5, rint(-1.5));   // 반올림 
		out.printf("ceil(%3.1f)=%f%n",    -1.5, ceil(-1.5));   // 올림 
		out.printf("floor(%3.1f)=%f%n",   -1.5, floor(-1.5));  // 버림 
	}
}
```

위 코드의 출력 결과는 다음과 같다.

```
round(90.7552)=91
round(9075.52)=9076
round(9075.52)/100  =90
round(9075.52)/100.0=90.76

ceil(1.1)=2.0
floor(1.5)=1.0
round(1.1)=1
round(1.5)=2
rint(1.5)=2.000000
round(-1.5)=-1
rint(-1.5)=-2.000000
ceil(-1.5)=-1.000000
floor(-1.5)=-2.000000
```

rint()도 round()처럼 소수점 첫째 자리에서 반올림하지만, 반환값이 double이다.

```java
out.printf("round(%3.1f)=%d%n", 1.5, round(1.5));    // 반환값이 int
out.printf("rint(%3.1f)=%f%n", 1.5, rint(1.5));      // 반환값이 double
```

음수를 반올림할 때 round(-1.5)의 결과는 -2가 아니라 -1이다. round()는 항상 가까운 큰 정수로 반올림하기 때문이다. rint()도 가장 가까운 정수로 반올림하지만, 1.5와 같이 두 정수의 정가운데 있는 값은 짝수 정수를 결과로 반환한다. 예를 들어, -1.5는 -1.0과 -2.0의 중간에 있으므로 rint(-1.5)는 가까운 정수 -1.0과 -2.0 중에서 짝수인 -2.0을 반환한다.

```java
out.printf("round(%3.1f)=%d%n", -1.5, round(-1.5));   // -1
out.printf("rint(%3.1f)=%f%n", -1.5, rint(-1.5));     // -2.0
```

음수에서는 양수와 달리 -1.5를 버림(floor)하면 -2.0이 된다.

```java
out.printf("ceil(%3.1f)=%f%n", -1.5, ceil(-1.5));     // -1.0
out.printf("floor(%3.1f)=%f%n", -1.5, floor(-1.5));   // -2.0
```
