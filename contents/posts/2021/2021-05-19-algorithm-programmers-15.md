---
title: "[프로그래머스] 이중우선순위큐"
date: 2021-05-19
update: 2021-05-19
tags:
  - Algorithm
  - Programmers
---

> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/42628>

### 문제 설명

이중 우선순위 큐는 다음 연산을 할 수 있는 자료구조를 말합니다.

|명령어|수신 탑(높이)|
|-|-|
|I|숫자	큐에 주어진 숫자를 삽입합니다.|
|D 1|큐에서 최댓값을 삭제합니다.|
|D -1|큐에서 최솟값을 삭제합니다.|

이중 우선순위 큐가 할 연산 operations가 매개변수로 주어질 때, 모든 연산을 처리한 후 큐가 비어있으면 [0,0] 비어있지 않으면 [최댓값, 최솟값]을 return 하도록 solution 함수를 구현해주세요.

### 제한사항

- operations는 길이가 1 이상 1,000,000 이하인 문자열 배열입니다.
- operations의 원소는 큐가 수행할 연산을 나타냅니다.
    - 원소는 “명령어 데이터” 형식으로 주어집니다.- 최댓값/최솟값을 삭제하는 연산에서 최댓값/최솟값이 둘 이상인 경우, 하나만 삭제합니다.
- 빈 큐에 데이터를 삭제하라는 연산이 주어질 경우, 해당 연산은 무시합니다.

### 입출력 예

|operations|return|
|-|-|
|["I 16", "D 1"]|[0, 0]|
|["I 7", "I 5", "I -5", "D -1"]|[7, 5]|

- 16을 삽입 후 최댓값을 삭제합니다. 비어있으므로 [0,0]을 반환합니다.
- 7,5,-5를 삽입 후 최솟값을 삭제합니다. 최대값 7, 최소값 5를 반환합니다.

### 내 풀이

최댓값과 최솟값을 출력해야 하므로
1. 정렬
2. 앞에서 꺼내기 (정렬하므로 최솟값은 앞에 위치)
3. 뒤에서 꺼내기 (정렬하므로 최댓값은 뒤에 위치)
가 필요한 자료구조로 구현해야 한다고 생각했다.

하지만 우선순위 큐에서는 1번은 가능하지만, 2번과 3번은 따로 처리를 해줘야 하기 때문에 LinkedList를 사용해 삽입이 이루어질 때마다 정렬을 하기로 했다.

1) I

값을 삽입 후 정렬한다.

```java
if (c == 'I') {
    list.add(i);
    Collections.sort(list);
}
```

2) D

큐가 비어있으면 해당 연산을 무시하라는 제한사항이 있으므로, `!list.isEmpty()`의 조건을 넣고, 1이면 뒤에 있는 값을, -1이면 앞에 있는 값을 삭제하도록 한다.

```java
else if (c == 'D' && !list.isEmpty()) {
    if (i == 1) {
        list.pollLast();
    } else if (i == -1) {
        list.pollFirst();
    }
}
```

### 전체 코드

```java
import java.util.*;
class Solution {
    public int[] solution(String[] operations) {
        int[] answer = new int[2];
        LinkedList<Integer> list = new LinkedList<>();

        for (String s : operations) {
            char c = s.charAt(0);
            int i = Integer.parseInt(s.substring(2));
            if (c == 'I') {
                list.add(i);
                Collections.sort(list);
            }
            else if (c == 'D' && !list.isEmpty()) {
                if (i == 1) {
                    list.pollLast();
                } else if (i == -1) {
                    list.pollFirst();
                }
            }
        }
        if (!list.isEmpty()) {
            answer[0] = list.peekLast();
            answer[1] = list.peekFirst();
        }

        return answer;
    }
}
```

