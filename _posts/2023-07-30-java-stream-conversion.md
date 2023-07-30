---
layout: post
title: 스트림의 변환
description: Java의 정석 기초편 ch. 14 람다와 스트림
categories: Java
date: 2023-07-30 22:54:00 +0900
---
스트림 간의 변환을 할 때 사용하는 메서드들을 아래에 표로 정리했다.

<table>
    <tr>
        <th>from</th>
        <th>to</th>
        <th>변환 메서드</th>
    </tr>
    <tr>
        <td colspan="3">1. 스트림 → 기본형 스트림</td>
    </tr>
    <tr>
        <td>Stream&#60;T&#62;</td>
        <td>IntStream<br>LongStream<br>DoubleStream</td>
        <td>mapToInt(ToIntFunction&#60;T&#62; mapper)<br>mapToLong(ToLongFunction&#60;T&#62; mapper)<br>mapToDouble(ToDoubleFunction&#60;T&#62; mapper)</td>
    </tr>
    <tr>
        <td colspan="3">2. 기본형 스트림 → 스트림</td>
    </tr>
    <tr>
        <td rowspan="2">IntStream<br>LongStream<br>DoubleStream</td>
        <td>Stream&#60;Integer&#62;<br>Stream&#60;Long&#62;<br>Stream&#60;Double&#62;</td>
        <td>boxed</td>
    </tr>
    <tr>
        <td>Stream&#60;U&#62;</td>
        <td>mapToObj(DoubleFunction&#60;U&#62; mapper)</td>
    </tr>
    <tr>
        <td colspan="3">3. 기본형 스트림 → 기본형 스트림</td>
    </tr>
    <tr>
        <td>IntStream<br>LongStream<br>DoubleStream</td>
        <td>LongStream<br>DoubleStream</td>
        <td>asLongStream()<br>asDoubleStream()</td>
    </tr>
    <tr>
        <td colspan="3">4. 스트림 → 부분 스트림</td>
    </tr>
    <tr>
        <td>Stream&#60;T&#62;<br>IntStream</td>
        <td>Stream&#60;T&#62;<br>IntStream</td>
        <td>skip(long n)<br>limit(long maxSize)</td>
    </tr>
    <tr>
        <td colspan="3">5. 두 개의 스트림 → 스트림</td>
    </tr>
    <tr>
        <td>Stream&#60;T&#62;, Stream&#60;T&#62;</td>
        <td>Stream&#60;T&#62;</td>
        <td>concat(Stream&#60;T&#62; a, Stream&#60;T&#62; b)</td>
    </tr>
    <tr>
        <td>IntStream, IntStream</td>
        <td>IntStream</td>
        <td>concat(IntStream a, IntStream b)</td>
    </tr>
    <tr>
        <td>LongStream, LongStream</td>
        <td>LongStream</td>
        <td>concat(LongStream a, LongStream b)</td>
    </tr>
    <tr>
        <td>DoubleStream, DoubleStream</td>
        <td>DoubleStream</td>
        <td>concat(DoubleStream a, DoubleStream b)</td>
    </tr>
    <tr>
        <td colspan="3">6. 스트림의 스트림 → 스트림</td>
    </tr>
    <tr>
        <td>Stream&#60;Stream&#60;T&#62;&#62;</td>
        <td>Stream&#60;T&#62;</td>
        <td>flatMap(Function mapper)</td>
    </tr>
    <tr>
        <td>Stream&#60;IntStream&#62;</td>
        <td>IntStream</td>
        <td>flatMapToInt(Function mapper)</td>
    </tr>
    <tr>
        <td>Stream&#60;LongStream&#62;</td>
        <td>LongStream</td>
        <td>flatMapToLong(Function mapper)</td>
    </tr>
    <tr>
        <td>Stream&#60;DoubleStream&#62;</td>
        <td>DoubleStream</td>
        <td>flatMapToDouble(Function mapper)</td>
    </tr>
    <tr>
        <td colspan="3">7. 스트림 → 병렬 스트림</td>
    </tr>
    <tr>
        <td>Stream&#60;T&#62;<br>IntStream<br>LongStream<br>DoubleStream</td>
        <td>Stream&#60;T&#62;<br>IntStream<br>LongStream<br>DoubleStream</td>
        <td>parallel()&nbsp;&nbsp;&nbsp;&nbsp;// 스트림 → 병렬 스트림<br>sequential()&nbsp;&nbsp;&nbsp;&nbsp;// 병렬 스트림 → 스트림</td>
    </tr>
    <tr>
        <td colspan="3">8. 스트림 → 컬렉션</td>
    </tr>
    <tr>
        <td rowspan="3">Stream&#60;T&#62;<br>IntStream<br>LongStream<br>DoubleStream</td>
        <td>Collection&#60;T&#62;</td>
        <td>collect(Collectors.toCollection(Supplier factory))</td>
    </tr>
    <tr>
        <td>List&#60;T&#62;</td>
        <td>collect(Collectors.toList())</td>
    </tr>
    <tr>
        <td>Set&#60;T&#62;</td>
        <td>collect(Collectors.toSet())</td>
    </tr>
    <tr>
        <td colspan="3">9. 컬렉션 → 스트림</td>
    </tr>
    <tr>
        <td>Collection&#60;T&#62;, List&#60;T&#62;, Set&#60;T&#62;</td>
        <td>Stream&#60;T&#62;</td>
        <td>stream()</td>
    </tr>
    <tr>
        <td colspan="3">10. 스트림 → Map</td>
    </tr>
    <tr>
        <td>Stream&#60;T&#62;<br>IntStream<br>LongStream<br>DoubleStream</td>
        <td>Map&#60;K, V&#62;</td>
        <td>collect(Collectors.toMap(Function key, Function value))<br>collect(Collectors.toMap(Function k, Function v, BinaryOperator))<br>collect(Collectors.toMap(Function k, Function v, BinaryOperator merge, Supplier mapSupplier))</td>
    </tr>
    <tr>
        <td colspan="3">11. 스트림 → 배열</td>
    </tr>
    <tr>
        <td rowspan="2">Stream&#60;T&#62;</td>
        <td>Object[]</td>
        <td>toArray()</td>
    </tr>
    <tr>
        <td>T[]</td>
        <td>toArray(IntFunction&#60;A[]&#62; generator)</td>
    </tr>
    <tr>
        <td>IntStream<br>LongStream<br>DoubleStream</td>
        <td>int[]<br>long[]<br>double[]</td>
        <td>toArray()</td>
    </tr>
</table>