---
layout: post
title: 쓰레드 그룹(thread group)
description: Java의 정석 기초편 ch. 13 쓰레드
categories: Java
date: 2023-07-05 14:32:00 +0900
---
쓰레드 그룹(thread group)은 서로 관련된 쓰레드를 그룹으로 다루기 위한 것으로, 폴더를 생성해서 관련된 파일들을 함께 넣어서 관리하는 것처럼 쓰레드 그룹을 생성해서 쓰레드를 그룹으로 묶어서 관리할 수 있다.

또한 폴더 안에 폴더를 생성할 수 있듯이 쓰레드 그룹에 다른 쓰레드 그룹을 포함시킬 수 있다. 사실 쓰레드 그룹은 보안상의 이유로 도입된 개념으로, 자신이 속한 쓰레드 그룹이나 하위 쓰레드 그룹은 변경할 수 있지만 다른 쓰레드 그룹의 쓰레드를 변경할 수는 없다.

쓰레드를 쓰레드 그룹에 포함시키려면 Thread의 생성자를 이용하면 된다.

```java
Thread(ThreadGroup group, String name)
Thread(ThreadGroup group, Runnable target)
Thread(ThreadGroup group, Runnable target, String name)
Thread(ThreadGroup group, Runnable target, String name, long stackSize)
```

모든 쓰레드는 반드시 쓰레드 그룹에 포함되어 있어야 하기 때문에, 위와 같이 쓰레드 그룹을 지정하지 않고 생성한 쓰레드는 기본적으로 자신을 생성한 쓰레드와 같은 쓰레드 그룹에 속하게 된다.

자바 어플리케이션이 실행되면, JVM은 main과 system이라는 쓰레드 그룹을 만들고 JVM 운영에 필요한 쓰레드들을 생성해서 이 쓰레드 그룹에 포함시킨다. 예를 들어 main메서드를 수행하는 main이라는 이름의 쓰레드는 main쓰레드 그룹에 속하고, 가비지컬렉션을 수행하는 Finalizer쓰레드는 system쓰레드 그룹에 속한다. 

우리가 생성하는 모든 쓰레드 그룹은 main쓰레드 그룹의 하위 쓰레드 그룹이 되며, 쓰레드 그룹을 지정하지 않고 생성한 쓰레드는 자동적으로 main쓰레드 그룹에 속하게 된다.

그 외에 Thread의 쓰레드 그룹과 관련된 메서드는 다음과 같다.

```java
ThreadGroup getThreadGroup()    // 쓰레드 자신이 속한 쓰레드 그룹을 반환한다.
void uncaughtException(Thread t, Throwable e)   // 처리되지 않은 예외에 의해 쓰레드 그룹의 쓰레드가 실행이 종료되었을 때, JVM에 의해 이 메서드가 자동적으로 호출된다.
```


## 쓰레드 그룹(thread group)의 메서드

<table>
    <tr>
        <th>생성자 / 메서드</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>ThreadGroup(String name)</td>
        <td>지정된 이름의 새로운 쓰레드 그룹을 생성</td>
    </tr>
    <tr>
        <td>ThreadGroup(ThreadGroup parent, String name)</td>
        <td>지정된 쓰레드 그룹에 포함되는 새로운 쓰레드 그룹을 생성</td>
    </tr>
    <tr>
        <td>int activeCount()</td>
        <td>쓰레드 그룹에 포함된 활성 상태에 있는 쓰레드의 수를 반환</td>
    </tr>
    <tr>
        <td>int activeGroupCount()</td>
        <td>쓰레드 그룹에 포함된 활성 상태에 있는 쓰레드 그룹의 수를 반환</td>
    </tr>
    <tr>
        <td>void checkAccess()</td>
        <td>현재 실행중인 쓰레드가 쓰레드 그룹을 변경할 권한이 있는지 체크. 만일 권한이 없다면 SecurityException을 발생시킨다.</td>
    </tr>
    <tr>
        <td>void destroy()</td>
        <td>쓰레드 그룹과 하위 쓰레드 그룹까지 모두 삭제한다. 단, 쓰레드 그룹이나 하위 쓰레드 그룹이 비어있어야 한다.</td>
    </tr>
    <tr>
        <td>int enumerate(Thread[] list)<br>int enumerate(Thread[] list, boolean recurse)<br>int enumerate(ThreadGroup[] list)<br>int enumerate(ThreadGroup[] list, boolean recurse)</td>
        <td>쓰레드 그룹에 속한 쓰레드 또는 하위 쓰레드 그룹의 목록을 지정된 배열에 담고 그 개수를 반환.<br>두 번째 매개변수인 recurse의 값을 true로 하면 쓰레드 그룹에 속한 하위 쓰레드 그룹에 쓰레드 또는 쓰레드 그룹까지 배열에 담는다.</td>
    </tr>
    <tr>
        <td>int getMaxPriority()</td>
        <td>쓰레드 그룹의 최대우선순위를 반환</td>
    </tr>
    <tr>
        <td>String getName()</td>
        <td>쓰레드 그룹의 이름을 반환</td>
    </tr>
    <tr>
        <td>ThreadGroup getParent()</td>
        <td>쓰레드 그룹의 상위 쓰레드 그룹을 반환</td>
    </tr>
    <tr>
        <td>void interrupt()</td>
        <td>쓰레드 그룹에 속한 모든 쓰레드를 interrupt</td>
    </tr>
    <tr>
        <td>boolean isDaemon()</td>
        <td>쓰레드 그룹이 데몬 쓰레드 그룹인지 확인</td>
    </tr>
    <tr>
        <td>boolean isDestroyed()</td>
        <td>쓰레드 그룹이 삭제되었는지 확인</td>
    </tr>
    <tr>
        <td>void list()</td>
        <td>쓰레드 그룹에 속한 쓰레드와 하위 쓰레드 그룹에 대한 정보를 출력</td>
    </tr>
    <tr>
        <td>boolean parentOf(ThreadGroup g)</td>
        <td>지정된 쓰레드 그룹의 상위 쓰레드 그룹인지 확인</td>
    </tr>
    <tr>
        <td>void setDaemon(boolean daemon)</td>
        <td>쓰레드 그룹을 데몬 쓰레드 그룹으로 설정/해제</td>
    </tr>
    <tr>
        <td>void setMaxPriority(int pri)</td>
        <td>쓰레드 그룹의 최대 우선순위를 설정</td>
    </tr>
</table>
