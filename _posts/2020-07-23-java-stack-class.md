---
layout: post
title: "Java - Stack 클래스"
categories: [Java]
---

### 스택 클래스
- 스택은 LIFO(Last In First Out) 구조를 가지는 자료구조
- util에 기본적으로 스택을 제공해주고 있다.


### 스택 생성하기
```java
Stack<Element> stack = new Stack<>();
```


### 메소드
```java
public boolean empty();
// stack의 값이 비었는지 확인
// 비었으면 true, 아니면 false

public Element push(Element item);
// 데이터 추가

public Element pop();
// 최근에 추가된(Top) 데이터 삭제

public Element peek();
// 최근에 추가된(Top) 데이터 조회

public int search(Object o)
// 인자값으로 받은 데이터의 위치 반환
```


### 예제
```java
import java.util.Stack;
public class Stack {
    public static void main(String[] args) {
        Stack<Integer> stack = new Stack<Integer>(); 
        stack.push(1);
        stack.push(2);
        stack.push(3);
        System.out.println(stack.peek());
        System.out.println(stack.pop());
        System.out.println(stack.search(1));
    }
}
```

결과
```java
3
3
2
```
