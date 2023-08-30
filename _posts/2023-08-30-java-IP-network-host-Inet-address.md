---
layout: post
title: IP주소(IP address), 네트워크 주소와 호스트 주소, InetAddress클래스
description: Java의 정석 기초편 ch. 16 네트워킹
categories: Java
date: 2023-08-30 13:17:00 +0900
---
IP주소는 컴퓨터(호스트, host)를 구별하는 데 사용되는 고유한 값으로 인터넷에 연결된 모든 컴퓨터는 IP주소를 갖는다. IP주소는 4 byte(32 bit)의 정수로 구성되어 있으며, 4개의 정수가 마침표를 구분자로 'a, b, c, d'와 같은 형식으로 표현된다. 여기서 a, b, c, d는 부호 없는 1 byte값, 즉 0~255 사이의 정수이다.

IP주소는 다시 네트워크주소와 호스트주소로 나눌 수 있는데, 32 bit(4 byte)의 IP주소 중에서 네트워크주소와 호스트주소가 각각 몇 bit를 차지하는 지는 네트워크를 어떻게 구성하였는지에 따라 달라진다. 그리고 서로 다른 두 호스트의 IP주소의 네트워크주소가 같다는 것은 두 호스트가 같은 네트워크에 포함되어 있다는 것을 의미한다.

Windows OS에서 호스트의 IP주소를 확인하려면 콘솔에서 ipconfig.exe를 실행시키면 된다. Mac OS에서는 ifconfig를 터미널에 입력하면 알 수 있다.

교재에 나온 예시는 다음과 같다.

```
C:\Documents and Settings\Administrator>ipconfig

Windows IP Configuration

Ethernet adapter 로컬 영역 연결:

        Connection-specific DNS Suffix  .   :
        IP Address. .   .   .   .   .   .   : 192.168.10.100
        Subnet Mask .   .   .   .   .   .   : 255.255.255.0
        Default Gateway .   .   .   .   .   : 192.168.10.1

C:\Documents and Settings\Administrator>        
```


## 네트워크 주소와 호스트 주소

앞서 ipconfig.exe를 실행해서 얻은 IP주소와 서브넷 마스크를 2진수로 표현하면 다음과 같다.

![drawio drawio (8)](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/fec9481e-edf8-42bb-bd5a-480a7aaa873c)

IP주소와 서브넷 마스크를 비트연산자 '&'로 연산하면 IP주소에서 네트워크 주소만을 뽑아낼 수 있다.

![drawio drawio (9)](https://github.com/johnkdk609/johnkdk609.github.io/assets/88493727/8bddf758-089a-43c0-8ba0-4cc51b0a1dea)

'&'연산자는 bit의 값이 모두 1일 때만 1을 결과로 얻기 때문에 IP주소의 마지막 8 bit는 모두 0이 되었다. 이 결과로부터 IP주소 192.168.10.100의 네트워크 주소는 24 bit(192.168.10)이라는 것과 호스트 주소는 마지막 8 bit(100)이라는 것을 알 수 있다.

IP주소에서 네트워크주소가 차지하는 자리수가 많을수록 호스트 주소의 범위가 줄어들기 때문에 네트워크의 규모가 작아진다. 이 경우 호스트 주소의 자리수가 8자리이기 때문에 256개(2⁸)의 호스트만 이 네트워크에 포함될 수 있다.

호스트 주소가 0인 것은 네트워크 자신을 나타내고, 255는 브로드캐스트 주소로 사용되기 때문에 실제로는 네트워크에 포함 가능한 호스트 개수는 254개이다.

이처럼 IP주소와 서브넷 마스크를 '&'연산하면 네트워크 주소를 얻어낼 수 있어서 서로 다른 두 호스트의 IP주소를 서브넷 마스크로 '&'연산을 수행해서 비교하면 이 두 호스트가 같은 네트워크 상에 존재하는지의 여부를 쉽게 확인할 수 있다.


## InetAddress클래스

자바에서는 IP주소를 다루기 위한 클래스로 InetAddress를 제공하며 다음과 같은 메서드가 정의되어 있다.

<table>
    <tr>
        <th>메서드</th>
        <th>설명</th>
    </tr>
    <tr>
        <td>byte[] getAddress()</td>
        <td>IP주소를 byte배열로 반환한다.</td>
    </tr>
    <tr>
        <td>static InetAddress[] getAllByName(String host)</td>
        <td>도메인명(host)에 지정된 모든 호스트의 IP주소를 배열에 담아 반환한다.</td>
    </tr>
    <tr>
        <td>static InetAddress getByAddress(byte[] addr)</td>
        <td>byte배열을 통해 IP주소를 얻는다.</td>
    </tr>
    <tr>
        <td>static InetAddress getByName(String host)</td>
        <td>도메인명(host)을 통해 IP주소를 얻는다.</td>
    </tr>
    <tr>
        <td>String getCanonicalHostName()</td>
        <td>FQDN(fully qualified domain name)을 반환한다.</td>
    </tr>
    <tr>
        <td>String getHostAddress()</td>
        <td>호스트의 IP주소를 반환한다.</td>
    </tr>
    <tr>
        <td>String getHostName()</td>
        <td>호스트의 이름을 반환한다.</td>
    </tr>
    <tr>
        <td>static InetAddress getLocalHost()</td>
        <td>지역 호스트의 IP주소를 반환한다.</td>
    </tr>
    <tr>
        <td>boolean isMulticastAddress()</td>
        <td>IP주소가 멀티캐스트 주소인지 알려준다.</td>
    </tr>
    <tr>
        <td>boolean isLoopbackAddress()</td>
        <td>IP주소가 loopback주소(127.0.0.1)인지 알려준다.</td>
    </tr>
</table>


## InetAddress클래스 예제

```java
import java.net.*;
import java.util.*;

class Ex16_1 {
	public static void main(String[] args) {
		InetAddress ip = null;
		InetAddress[] ipArr = null;

		try {
			ip = InetAddress.getByName("www.naver.com");
			System.out.println("getHostName() :"+ip.getHostName());
			System.out.println("getHostAddress() :"+ip.getHostAddress());
			System.out.println("toString() :"+ip.toString());

			byte[] ipAddr = ip.getAddress();
			System.out.println("getAddress() :"+Arrays.toString(ipAddr));

			String result = "";
			for(int i=0; i < ipAddr.length;i++)
				result += (ipAddr[i] < 0 ? ipAddr[i] + 256 : ipAddr[i])+".";
			System.out.println("getAddress()+256 :"+result);
			System.out.println();
		} catch (UnknownHostException e) {
			e.printStackTrace();
		}

		try {
			ip = InetAddress.getLocalHost();
			System.out.println("getHostName() :"+ip.getHostName());
			System.out.println("getHostAddress() :"+ip.getHostAddress());
			System.out.println();
		} catch (UnknownHostException e) {
			e.printStackTrace();
		}
		
		try {
			ipArr = InetAddress.getAllByName("www.naver.com");

			for(int i=0; i < ipArr.length; i++)
				System.out.println("ipArr["+i+"] :" + ipArr[i]);
		} catch (UnknownHostException e) {
			e.printStackTrace();
		}
	} // main
}
```

위 코드의 출력 결과는 다음과 같다.

```
getHostName() :www.naver.com
getHostAddress() :223.130.200.104
toString() :www.naver.com/223.130.200.104
getAddress() :[-33, -126, -56, 104]
getAddress()+256 :223.130.200.104.

getHostName() :DK-MacBookPro.local
getHostAddress() :127.0.0.1

ipArr[0] :www.naver.com/223.130.200.104
ipArr[1] :www.naver.com/223.130.195.95
```

하나의 도메인명(www.naver.com)에 여러 IP주소가 맵핑될 수도 있고 또 그 반대의 경우도 가능하기 때문에 전자의 경우 getAllByName()을 통해 모든 IP주소를 얻을 수 있다.

그리고 getLocalHost()를 사용하면 호스트명과 IP주소를 알아낼 수 있다.