---
title: "[프로그래머스] 3진법 뒤집기"
date: 2021-07-16
update: 2021-07-16
tags:
  - Algorithm
  - Programmers
---


> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/68935>

### 문제 설명

자연수 n이 매개변수로 주어집니다. n을 3진법 상에서 앞뒤로 뒤집은 후, 이를 다시 10진법으로 표현한 수를 return 하도록 solution 함수를 완성해주세요.

### 제한사항

- n은 1 이상 100,000,000 이하인 자연수입니다.

### 입출력 예

|name|result|
|-|-|
|45|7|
|125|229|

### 내 풀이

문제 그대로 n을 3진법을 뒤집은 후, 이를 다시 10진법으로 표현하면 된다.
다만, 3진법을 나타낼 때 뒤에서 부터 나타내 뒤집는 단계를 생략하도록 했다.


```java
class Solution {
    public int solution(int n) {
        int answer = 0;
        String str = "";
        while (n > 0) {
            str += (n % 3);
            n /= 3;
        }
        for(int i=0; i<str.length(); i++) {
            answer += (str.charAt(str.length() - i - 1) - '0') * (int) Math.pow(3, i);
        }
        return answer;
    }
}
```
