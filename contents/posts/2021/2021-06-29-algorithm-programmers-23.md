---
title: "[프로그래머스] 조이스틱"
date: 2021-06-29
update: 2021-06-29
tags:
  - Algorithm
  - Programmers
---


> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/42860>

### 문제 설명

조이스틱으로 알파벳 이름을 완성하세요. 맨 처음엔 A로만 이루어져 있습니다.
ex) 완성해야 하는 이름이 세 글자면 AAA, 네 글자면 AAAA

조이스틱을 각 방향으로 움직이면 아래와 같습니다.

```
▲ - 다음 알파벳
▼ - 이전 알파벳 (A에서 아래쪽으로 이동하면 Z로)
◀ - 커서를 왼쪽으로 이동 (첫 번째 위치에서 왼쪽으로 이동하면 마지막 문자에 커서)
▶ - 커서를 오른쪽으로 이동
```

예를 들어 아래의 방법으로 "JAZ"를 만들 수 있습니다.

```
- 첫 번째 위치에서 조이스틱을 위로 9번 조작하여 J를 완성합니다.
- 조이스틱을 왼쪽으로 1번 조작하여 커서를 마지막 문자 위치로 이동시킵니다.
- 마지막 위치에서 조이스틱을 아래로 1번 조작하여 Z를 완성합니다.
```

따라서 11번 이동시켜 "JAZ"를 만들 수 있고, 이때가 최소 이동입니다.
만들고자 하는 이름 name이 매개변수로 주어질 때, 이름에 대해 조이스틱 조작 횟수의 최솟값을 return 하도록 solution 함수를 만드세요.


### 제한사항

- name은 알파벳 대문자로만 이루어져 있습니다.
- name의 길이는 1 이상 20 이하입니다.

### 입출력 예

|name|return|
|-|-|
|"JEROEN"|56|
|"JAN"|23|

### 내 풀이

너무 어렵게 생각한 탓에 시간 내에 다 풀지 못하고 다른 사람의 풀이를 참고했다.
내일 다시 한번 풀어보자.

출처 - [https://hellodavid.tistory.com/4]

```java
import java.util.*;
class Solution {
    public int solution(String name) {
        int answer = 0;
        int len = name.length();

        // 제일 짧은 좌, 우 이동은 그냥 맨 오른쪽으로 이동할 때
        int min = len - 1;

        for (int i = 0; i < len; i++) {
            // 조이스틱 상, 하 이동
            char c = name.charAt(i);
            int mov = (c - 'A' < 'Z' - c + 1) ? (c - 'A') : ('Z' - c + 1);
            answer += mov;

            // 조이스틱 좌, 우 이동
            int nextIndex = i + 1;
            // 다음 단어가 A이고, 단어가 끝나기 전까지 nextIndex 증가
            while (nextIndex < len && name.charAt(nextIndex) == 'A')
            nextIndex++;

            min = Math.min(min, (i * 2) + len - nextIndex);
        }

        answer += min;

        return answer;
    }
}
```
