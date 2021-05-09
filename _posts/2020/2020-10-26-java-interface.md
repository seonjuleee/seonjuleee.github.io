---
layout: post
title: "Java - 인터페이스"
categories: [Java]
---

### 인터페이스(interface)
인터페이스는 다른 클래스를 작성할 때 기본이 되는 틀을 제공하면서, 다른 클래스 사이의 중간 매개 역할까지 담당하는 일종의 추상 클래스를 의미한다.

### 인터페이스 선언
```java
public interface Animal {
	public /*abstract*/ void cry();
}
```
인터페이스의 모든 필드는 `public static final`이어야 하고, 모든 메소드는 `public abstract`여야 한다. 하지만 이 제어자를 생략할 수 있다.

### 인터페이스 구현
```java
class Cat implements Animal {
	public void cry() {
		System.out.println("야옹");
	}
}
```
`implements`를 이용하여 인터페이스를 구현할 수 있다.

```java
public static void main(String[] args) {
    Cat cat = new Cat();
    
    cat.cry();
}
```
실행 결과
```
야옹
```

### 출처
- <http://tcpschool.com/java/java_polymorphism_interface>