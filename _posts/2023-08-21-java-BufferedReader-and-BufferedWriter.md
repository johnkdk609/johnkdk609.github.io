---
layout: post
title: BufferedReader와 BufferedWriter
description: Java의 정석 기초편 ch. 15 입출력
categories: Java
date: 2023-08-21 21:02:00 +0900
---
BufferedReader/BufferedWriter는 버퍼를 이용해서 입출력의 효율을 높일 수 있도록 해주는 역할을 한다. 버퍼를 이용하면 입출력의 효율이 비교할 수 없을 정도로 좋아지기 때문에 사용하는 것이 좋다.

BufferedReader의 readLine()을 사용하면 데이터를 라인 단위로 읽을 수 있고 BufferedWriter는 newLine()이라는 줄바꿈 해주는 메서드를 가지고 있다.

```java
import java.io.*;

class Ex15_11 {
	public static void main(String[] args) {
		try {
			FileReader fr = new FileReader("Ex15_11.java");
//		이클립스에서는 윗 줄 대신 아래 줄 입력 
// 			FileReader fr = new FileReader(".\\src\\Ex15_11.java");
			BufferedReader br = new BufferedReader(fr);

			String line = "";
			for(int i=1;(line = br.readLine())!=null;i++) { 
				//  ";"를 포함한 라인을 출력한다. 
				if(line.indexOf(";")!=-1)	
					 System.out.println(i+":"+line);
			}
                     
			br.close();
		} catch(IOException e) {}
	} // main
}
```

BufferedReader의 readLine()을 이용해서 파일을 라인 단위로 읽은 다음 indexOf()를 이용해서 ';'를 포함하고 있는지 확인하여 출력하는 예제이다. 파일에서 특정 문자 또는 문자열을 포함한 라인을 쉽게 찾아낼 수 있음을 보여 준다.