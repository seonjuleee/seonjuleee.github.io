---
title: "[프로그래머스] H-Index"
date: 2021-05-23
update: 2021-05-23
tags:
  - Algorithm
  - Programmers
---


> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/42747>

### 문제 설명

H-Index는 과학자의 생산성과 영향력을 나타내는 지표입니다. 어느 과학자의 H-Index를 나타내는 값인 h를 구하려고 합니다. 위키백과1에 따르면, H-Index는 다음과 같이 구합니다.

어떤 과학자가 발표한 논문 `n`편 중, `h`번 이상 인용된 논문이 `h`편 이상이고 나머지 논문이 h번 이하 인용되었다면 `h`의 최댓값이 이 과학자의 H-Index입니다.

어떤 과학자가 발표한 논문의 인용 횟수를 담은 배열 citations가 매개변수로 주어질 때, 이 과학자의 H-Index를 return 하도록 solution 함수를 작성해주세요.

### 제한사항

- 과학자가 발표한 논문의 수는 1편 이상 1,000편 이하입니다.
- 논문별 인용 횟수는 0회 이상 10,000회 이하입니다.

### 입출력 예

|citations|return|
|-|-|
|[3, 0, 6, 1, 5]|3|

이 과학자가 발표한 논문의 수는 5편이고, 그중 3편의 논문은 3회 이상 인용되었습니다. 그리고 나머지 2편의 논문은 3회 이하 인용되었기 때문에 이 과학자의 H-Index는 3입니다.

### 내 풀이

우선 정렬을 해 `[0, 1, 3, 5, 6]`으로 만든다.

```java
Collections.sort(list);
```

하나씩 탐색하면서 h편 이하로 인용된 최댓값 h를 구한다.

```java
int maxH = 0;
int count = 0;
for (int h=1; h<=list.get(list.size() - 1); h++) {
    for (; count<list.size(); count++) {
        if (h < list.get(count)) break;
    }
    if (h >= count) maxH = Math.max(h, maxH);
}
answer = maxH;
```

같은 방법으로 하나씩 탐색하면서 h편 이상으로 인용된 최댓값 h를 구한다.

```java
count = list.size();
maxH = 0;
for (int h=1; h<=list.get(list.size() - 1); h++) {
    for (int j=list.size() - count; j<list.size(); j++, count--) {
        if (h <= list.get(j)) break;
    }
    if (h <= count) maxH = Math.max(h, maxH);
}
answer = (maxH < answer) ? maxH : answer;
```

### 전체 코드
```java
import java.util.*;
class Solution {
    public int solution(int[] citations) {
        int answer = 0;
        List<Integer> list = new ArrayList<>();
        for (int i : citations) {
            list.add(i);
        }
        Collections.sort(list);
        // h편 이하로 인용된 최댓값 h 구하기
        int maxH = 0;
        int count = 0;
        for (int h=1; h<=list.get(list.size() - 1); h++) {
            for (; count<list.size(); count++) {
                if (h < list.get(count)) break;
            }
            if (h >= count) maxH = Math.max(h, maxH);
        }
        answer = maxH;
        // h편 이상으로 인용된 최댓값 h 구하기
        count = list.size();
        maxH = 0;
        for (int h=1; h<=list.get(list.size() - 1); h++) {
            for (int j=list.size() - count; j<list.size(); j++, count--) {
                if (h <= list.get(j)) break;
            }
            if (h <= count) maxH = Math.max(h, maxH);
        }
        answer = (maxH < answer) ? maxH : answer;
        return answer;
    }
}
```

### 다른 사람의 풀이

i가 증가하면서 i보다 높은 오른쪽의 원소들의 개수와 i보다 낮은 왼쪽의 원소들의 개수를 비교해 낮은 값을 가지는 것이 바로 그 때의 h값이 될 것이다.

그 h값이 가장 최대일 때가 정답값이 된다.

```java
import java.util.Arrays;

class Solution {
    public int solution(int[] citations) {
        int answer = 0;
        Arrays.sort(citations);
        for(int i=0; i<citations.length; i++){
            int smaller = Math.min(citations[i], citations.length-i);
            answer = Math.max(answer, smaller);
        }
        return answer;
    }
}
```