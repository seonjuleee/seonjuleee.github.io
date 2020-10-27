---
layout: post
title: "Java - StringBuilder"
categories: [Java]
---

### String과 StringBuilder의 차이
String은 새로운 값을 할당할 때마다 클래스가 새로 생성하기 때문에 문자열을 더하면 더할수록 메모리에 쌓인다.
반면에 StringBuilder는 문자열을 더하면 메모리에 append하는 방식으로 클래스를 직접 생성하지 않아도 된다. 

이러한 이유 때문에 긴 문자열을 이용할 때에는 StringBuilder를 사용하는 것이 성능에 더 좋다.

### 사용
```java
public class Main {
	public static void main(String[] args) {
		StringBuilder sb = new StringBuilder();
		sb.append("a");
		sb.append("b");
		sb.append("c");
		System.out.println(sb);
	}
}
```
### 결과
```java
abc
```


출처: <https://novemberde.github.io/2017/04/15/String_0.html>