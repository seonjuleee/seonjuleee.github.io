---
title: "[프로그래머스] 모의고사"
date: 2021-05-26
update: 2021-05-26
tags:
  - Algorithm
  - Programmers
---


> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/42840>

### 문제 설명

수포자는 수학을 포기한 사람의 준말입니다. 수포자 삼인방은 모의고사에 수학 문제를 전부 찍으려 합니다. 수포자는 1번 문제부터 마지막 문제까지 다음과 같이 찍습니다.

1번 수포자가 찍는 방식: 1, 2, 3, 4, 5, 1, 2, 3, 4, 5, ...
2번 수포자가 찍는 방식: 2, 1, 2, 3, 2, 4, 2, 5, 2, 1, 2, 3, 2, 4, 2, 5, ...
3번 수포자가 찍는 방식: 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, ...

1번 문제부터 마지막 문제까지의 정답이 순서대로 들은 배열 answers가 주어졌을 때, 가장 많은 문제를 맞힌 사람이 누구인지 배열에 담아 return 하도록 solution 함수를 작성해주세요.

### 제한 조건

시험은 최대 10,000 문제로 구성되어있습니다.
문제의 정답은 1, 2, 3, 4, 5중 하나입니다.
가장 높은 점수를 받은 사람이 여럿일 경우, return하는 값을 오름차순 정렬해주세요.

### 입출력 예

|answers|return|
|[1,2,3,4,5]|[1]|
|[1,3,2,4,2]|[1,2,3]|

- 수포자 1은 모든 문제를 맞혔습니다.
- 수포자 2는 모든 문제를 틀렸습니다.
- 수포자 3은 모든 문제를 틀렸습니다.

따라서 가장 문제를 많이 맞힌 사람은 수포자 1입니다.

### 내 풀이

문제를 비교해서 count 배열에 각각 저장하는데, 문제와 찍은 답의 개수가 다르고 찍은 답의 개수가 계속해서 반복되므로 `인덱스 % (찍은 답의 개수)`로 나머지를 구해 정답과 비교한다.

```java
if (nums.get(j)[i % nums.get(j).length] == answers[i]) count[j]++;
```

count 중 가장 높은 수를 `max`에 저장하고, `max`값과 같은 count 값은 가장 많은 문제를 맞힌 사람이므로 answer에 저장한다.

```java
for (int i=0; i<3; i++) {
    if (max == count[i]) arr.add(i + 1);
}
```

### 전체 코드

```java
import java.util.*;
class Solution {
    public int[] solution(int[] answers) {
        ArrayList<int[]> nums = new ArrayList<>();
        nums.add(new int[] {1, 2, 3, 4, 5});
        nums.add(new int[] {2, 1, 2, 3, 2, 4, 2, 5});
        nums.add(new int[] {3, 3, 1, 1, 2, 2, 4, 4, 5, 5});
        int[] count = new int[3];
        int max = 0;
        for (int j=0; j<3; j++) {
            for (int i=0; i<answers.length; i++) {
                if (nums.get(j)[i % nums.get(j).length] == answers[i]) count[j]++;
            }
            max = Math.max(max, count[j]);
        }

        ArrayList<Integer> arr = new ArrayList<>();
        if (max == 0) return new int[] {};
        for (int i=0; i<3; i++) {
            if (max == count[i]) arr.add(i + 1);
        }
        int[] answer = new int[arr.size()];

        for(int i=0; i<arr.size(); i++) {
            answer[i] = arr.get(i);
        }

        return answer;
    }
}
```