---
layout: post
title: "[알고리즘][JAVA] 큐 클래스"
---

### 큐 클래스
- 큐는 FIFO(First In First Out) 구조를 가지는 자료구조


### 큐 생성하기
```java
Queue<Integer> queue = new LinkedList<>();
```


### 메소드

|   | throws exception | returns special value |
|-----|-----|-----|
| Insert | boolean add(e) | boolean offer(e) |
| Remove | E remove() | E poll() |
| Examine | E element() | E peek() |

queue 클래스의 메소드는 문제 상황에서 예외를 던지는지 아니면 값(null 또는 false)를 반환하는지에 따라 달라질 수 있다.


### 예제
```java
import java.util.LinkedList;
import java.util.Queue;
public class Stack {
    public static void main(String[] args) {
        Queue<Integer> queue = new LinkedList<Integer>();
        queue.add(1);
        queue.add(2);
        queue.add(3);
        System.out.println(queue.poll());
        System.out.println(queue.peek());
        System.out.println(queue.isEmpty());
    }
}
```

결과
```java
1
2
false
```
