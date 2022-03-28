---
title: "Java Collection"
date: 2021-05-22
update: 2021-05-22
tags:
  - Java
  - Algorithm
---

### Java Collection Framework의 상속 기본 구조

![Java Collection Framework]({{"/assets/images/java-collection.png"| relative_url}})

### 각 인터페이스의 특징

1. List
- LinkedList, Stack, Vector, ArrayList
- 순서가 있는 데이터의 집합
- 데이터의 중복을 허용

2. Set
- HashSet, TreeSet
- 순서를 유지하지 않는 데이터의 집합
- 데이터의 중복을 허용하지 않음

3. Map
- HashMap, TreeMap, HashTable, Properties
- 키(key)와 값(value)의 쌍으로 이루어진 데이터의 집합
- 순서는 유지되지 않음
- 키는 중복을 허용하지 않으며, 값은 중복을 허용
- List와 Set 인터페이스는 모두 Collection 인터페이스를 상속 받지만, 구조상의 차이로 인해 Map 인터페이스는 별도로 정의

### 메소드

```java
// (*)가 표시된 메소드는 선택적인 기능

boolean add(E e) // 요소 추가 (*)

void clear() // 모든 요소 제거 (*)

boolean contains(Object o) // 객체 포함하는지 확인

boolean equals(Object o) // 객체가 같은지를 확인

boolean isEmpty() // 비어있는지 확인

Iterator<E> iterator() // 반복자(iterator) 반환

boolean remove(Object o) // 객체 제거 (*)

int size() // 요소의 총 개수 반환

Object[] toArray() // 모든 요소를 Object 타입의 배열로 반환
```

### 출처
- <https://hwan1001.tistory.com/10>
- <http://tcpschool.com/java/java_collectionFramework_concept>
