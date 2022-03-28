---
title: "[프로그래머스] 두 개 뽑아서 더하기"
date: 2021-07-18
update: 2021-07-18
tags:
  - Algorithm
  - Programmers
---


> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/68644>

### 문제 설명

정수 배열 numbers가 주어집니다. numbers에서 서로 다른 인덱스에 있는 두 개의 수를 뽑아 더해서 만들 수 있는 모든 수를 배열에 오름차순으로 담아 return 하도록 solution 함수를 완성해주세요.

### 제한사항

- numbers의 길이는 2 이상 100 이하입니다.
    - numbers의 모든 수는 0 이상 100 이하입니다.


### 입출력 예

|numbers|result|
|-|-|
|[2,1,3,4,1]|[2,3,4,5,6,7]|
|[5,0,2,7]|[2,5,7,9,12]|

#### 입출력 예 #1

2 = 1 + 1 입니다. (1이 numbers에 두 개 있습니다.)
3 = 2 + 1 입니다.
4 = 1 + 3 입니다.
5 = 1 + 4 = 2 + 3 입니다.
6 = 2 + 4 입니다.
7 = 3 + 4 입니다.
따라서 [2,3,4,5,6,7] 을 return 해야 합니다.

#### 입출력 예 #2

2 = 0 + 2 입니다.
5 = 5 + 0 입니다.
7 = 0 + 7 = 5 + 2 입니다.
9 = 2 + 7 입니다.
12 = 5 + 7 입니다.
따라서 [2,5,7,9,12] 를 return 해야 합니다.

### 내 풀이

numbers의 수를 각각 두 개를 뽑아서 더해준 후 정렬하였다.
for문이 두 개여도 numbers의 길이가 100 이하이기 때문에 시간이 적게 걸리기 때문에 사용할 수 있었다.


```java
import java.util.*;
class Solution {
    public int[] solution(int[] numbers) {
        List<Integer> list = new ArrayList<>();
        for (int i=0; i<numbers.length - 1; i++) {
            for (int j=i+1; j<numbers.length; j++) {
                int tmp = numbers[i] + numbers[j];
                if(!list.contains(tmp)) {
                    list.add(tmp);
                }
            }
        }
        int[] answer = new int[list.size()];
        Collections.sort(list);
        for (int i=0; i<list.size(); i++) {
            answer[i] = list.get(i);
        }
        return answer;
    }
}
```
