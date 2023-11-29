---
title: Java - Assignment vs Equality vs Equals
author: Daehong
date: 2023-11-29
category: Jekyll
layout: post
mermaid: true
---

* 이건 한글로 번역하면, 할당 vs 동일 vs 동등이다.

<br>
### 할당 ( Assignment, = )
* 할당은 int a = 10; 이렇게 a라는 변수에 10을 주는 것을 할당이라고 하고, 영어로는 Assignment 라고 한다.
* = 으로 할당한다.

<br>
<br>

### 동일 ( Equality, == )
* 동일은 자료형을 비교할 때와, 객체를 비교할 때 두 가지로 나뉜다.
* 원시 타입의 경우 값을 비교하고, 객체 타입의 경우 객체 주소를 비교한다.
* == 으로 비교한다

<br>
<br>

### 동등 ( Equals(); )
* 동등은 equals 함수를 이용하여 비교하는 것이며, 객체 주소가 달라도 값이 같으면 동등하다고 이야기한다.
* 그러나 중요한 점은, 반드시 재정의 된 equals여야 된다는 것이다. (Override)

<br>
<br>

### 동일, 동등 개념으로 보는 String 객체
* 아래 코드는 정보처리기사 2023년 2회 문제이다.
	```java
	class Gisafirst {
		public static void main(String[] args) {
			String str1 = "Programming"; // true or false
			String str2 = "Programming";
			String str3 = new String("Programming");
			System.out.println(str1==str2);
			System.out.println(str1==str3);
			System.out.println(str1.equals(str3));
			System.out.println(str2.equals(str3));
		}
	}
	```
* 답은 true, false, true, true가 나온다.
* str1과 str2는 값은 같지만 서로 다른 객체일텐데 왜 ==가 true일까?
* 그 이유는 String str = "..."; 이렇게 선언할 경우 문자열 리터럴을 참조한다. 즉 str1과 str2는 같은 문자열 리터럴을 참조하고 있기 때문에, str1 = str2 와 같은 상태이다. 즉 둘이 참조하고 있는 주소가 같다. 좀 더 부연 설명을 하자면, new String으로 선언하면 힙 메모리 영역에 인스턴스를 생성하지만, str1 str2처럼 생성하면 String Constant Pool 라는 곳에 상수로 하나만 생성된다. ( 원래는 따로 영역이 있었지만, Java 7부터는 String Constant Pool도 힙 영역 안으로 들어왔다. )
* 마지막으로 str1이랑 str3은 왜 == 하면 false가 나오나요? 한다면 str3은 new String으로 새로운 객체를 생성했기 때문에 str1과 str3은 서로 다른 주소를 참조하므로 false가 맞다.
<br>
<br>
<br>
<br>
<br>