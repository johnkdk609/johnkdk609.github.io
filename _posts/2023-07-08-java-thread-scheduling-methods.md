---
layout: post
title: 쓰레드의 실행제어
description: Java의 정석 기초편 ch. 13 쓰레드
categories: Java
date: 2023-07-08 23:28:00 +0900
---
쓰레드 프로그래밍이 어려운 이유는 동기화(synchronization)와 스케줄링(scheduling) 때문이다. 앞서 우선순위를 통해 쓰레드간의 스케줄링을 하는 방법을 공부하기는 했지만, 이것만으로는 한참 부족하다. 효율적인 멀티쓰레드 프로그램을 만들기 위해서는 보다 정교한 스케줄링을 통해 프로세스에게 주어진 자원과 시간을 여러 쓰레드가 낭비없이 잘 사용하도록 프로그래밍 해야 한다.

쓰레드의 스케줄링을 잘하기 위해서는 쓰레드의 상태와 관련 메서드를 잘 알아야 하는데, 먼저 쓰레드의 스케줄링과 관련된 메서드는 다음과 같다.

<table>
    <tr>
        <th>메서드</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>static void sleep(long millis)<br>static void sleep(long millis, int nanos)</td>
        <td>지정된 시간(천분의 일초 단위)동안 쓰레드를 일시정지시킨다. 지정한 시간이 지나고 나면, 자동적으로 다시 실행대기상태가 된다.</td>
    </tr>
    <tr>
        <td>void join()<br>void join(long millis)<br>void join(long millis, int nanos)</td>
        <td>지정된 시간동안 쓰레드가 실행되도록 한다. 지정된 시간이 지나거나 작업이 종료되면 join()을 호출한 쓰레드로 다시 돌아와 실행을 계속한다.</td>
    </tr>
    <tr>
        <td>void interrupt()</td>
        <td>sleep()이나 join()에 의해 일시정지상태인 쓰레드를 깨워서 실행대기상태로 만든다. 해당 쓰레드에서는 InterruptedException이 발생함으로써 일시정지상태를 벗어나게 된다.</td>
    </tr>
    <tr>
        <td>void stop()</td>
        <td>쓰레드를 즉시 종료시킨다.</td>
    </tr>
    <tr>
        <td>void suspend()</td>
        <td>쓰레드를 일시정지시킨다. resume()을 호출하면 다시 실행대기상태가 된다.</td>
    </tr>
    <tr>
        <td>void resume()</td>
        <td>suspend()에 의해 일시정지상태에 있는 쓰레드를 실행대기상태로 만든다.</td>
    </tr>
    <tr>
        <td>static void yield()</td>
        <td>실행 중에 자신에게 주어진 실행시간을 다른 쓰레드에게 양보(yield)하고 자신은 실행대기상태가 된다.</td>
    </tr>
</table>

위 표는 쓰레드의 스케줄링과 관련한 메서드들이다.

(resume(), stop(), suspend()는 쓰레드를 교착상태(dead-lock)로 만들기 쉽기 때문에 deprecated 되었다.)