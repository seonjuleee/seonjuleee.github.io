---
title: "Java - Deque 클래스"
description: "Java - Deque Class"
date: 2020-08-04
update: 2020-08-04
tags:
  - Java
---

### 덱
Double-Ended Queue의 줄임말으로, 큐의 양쪽 끝에서 삽입과 삭제가 모두 발생할 수 있는 큐

어떤 쪽으로 입출력 하는지에 따라서 스택(Stack)과 큐(Queue)로도 사용가능하다.

### 덱 생성하기
```java
Deque<Integer> deque = new ArrayDeque<Integer>();
Deque<Integer> deque = new LinkedList<Integer>();
```

### 메소드
* First Element(Head)

|   | throws exception | returns special value |
|-----|-----|-----|
| Insert | void addFirst(e) | boolean offerFirst(e) |
| Remove | E removeFirst() | E pollFirst() |
| Examine | E getFirst() | E peekFirst() |

* Last Element(Tail)

|   | throws exception | returns special value |
|-----|-----|-----|
| Insert | void addLast(e) | boolean offerLast(e) |
| Remove | E removeLast() | E pollLast() |
| Examine | E getLast() | E peekLast() |


* 큐처럼 사용하는 메소드

```java
public boolean add(Element e)
// addLast()와 동일

public boolean offer(Element e)
// offerLast()와 동일

public Element remove()
// removeFirst()와 동일

public Element remove(Object o)
// removeFirstOccurrence()와 동일

public Element poll()
// pollFirst()와 동일

public Element peek()
// peekFist()와 동일
```

* 스택처럼 사용하는 메소드

```java
public void push(Element e)
// addFirst()와 동일. 덱을 스택으로 사용할 때 쓰임

public Element pop()
// removeFirst()와 동일. 덱을 스택으로 사용할 때 쓰임
```

* 기타 메소드

```java
public Element element()
// 덱에 있는 첫번째 element 리턴

public boolean contains(Object o)
// element가 포함되어 있는지 true/false 리턴

public int size()
// 덱의 element 개수를 리턴

public boolean removeFirstOccurrence(Object o)
// 덱의 앞쪽에서부터 탐색하여 Object와 동일한 첫번째 element 제거

public boolean removeLastOccurrence(Object o)
// 덱의 뒤쪽에서부터 탐색하여 Object와 동일한 첫번째 element 제거
```


* 예제
```java
import java.util.LinkedList;
import java.util.Deque;
public class Deque {
    public static void main(String[] args) {
        Deque<Integer> deque = new LinkedList<Integer>();
        deque.add(1);
        deque.addFirst(2);
        deque.addLast(3);
        System.out.println(deque.getFirst());
        System.out.println(deque.removeLast());
        System.out.println(deque.size());
    }
}
```

결과
```java
2
3
2
```