---
title: "[프로그래머스] 타겟 넘버"
date: 2021-04-28
update: 2021-04-28
tags:
  - Algorithm
  - Programmers
---

> 문제
> <https://programmers.co.kr/learn/courses/30/lessons/43165>

### 내 풀이
```java
class Solution {
    public int[] array;
    public int gTarget;
    public int answer = 0;
    
    public int solution(int[] numbers, int target) {
        array = numbers;
        gTarget = target;
        dfs(0, 0);
        return answer;
    }
    
    public void dfs(int i, int sum) {
        if (i >= array.length) {
            if (sum == gTarget) { 
                answer++;
            }
            return;
        } else {
            dfs(i + 1, sum + array[i]);
            dfs(i + 1, sum - array[i]);
        }
    }
}
```