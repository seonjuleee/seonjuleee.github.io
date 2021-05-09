---
layout: post
title: "[프로그래머스] 완주하지 못한 선수"
categories: [Algorithm]
---

> 문제
> <https://programmers.co.kr/learn/courses/30/lessons/42576>

처음에는 단순하게 participant 배열을 리스트에 추가하고, completion 배열을 리스트에서 제거하면 될 것이라고 생각했다.

### 내 풀이

```java
import java.util.*;

class Solution {
    public String solution(String[] participant, String[] completion) {
        List<String> list = new ArrayList<>();
        for(String p : participant) {
            list.add(p);
        }
        for(String c : completion) {
            list.remove(c);
        }
        String answer = list.get(0);
        return answer;
        
    }
}
```
정확도 테스트에서는 통과했지만, 효율성 테스트에서 시간 초과로 실패하였다.

### 다른 사람의 풀이 1

```java
import java.util.*;

class Solution {
    public String solution(String[] participant, String[] completion) {
        Arrays.sort(participant);
        Arrays.sort(completion);
        int i = 0;
        for(; i<completion.length; i++) {
            if(!participant[i].equals(completion[i])) {
                return participant[i];
            }
        }
        return participant[i];
    }
}
```
participant와 completion의 길이가 1씩 차이난다는 것을 이용하면 간단하게 풀 수 있는 문제였다.

### 다른 사람의 풀이 2

```java
import java.util.HashMap;

class Solution {
    public String solution(String[] participant, String[] completion) {
        String answer = "";
        HashMap<String, Integer> hm = new HashMap<>();
        for (String player : participant) hm.put(player, hm.getOrDefault(player, 0) + 1);
        for (String player : completion) hm.put(player, hm.get(player) - 1);

        for (String key : hm.keySet()) {
            if (hm.get(key) != 0){
                answer = key;
            }
        }
        return answer;
    }
}
```
HashMap을 사용해 깔끔하게 풀었다. 
getOrDefault 함수에 대해 처음 알게되었는데, 잊지 않도록 HashMap과 관련된 함수를 정리해 포스팅 해야겠다.

### 출처
[프로그래머스 코딩 테스트 연습](https://programmers.co.kr/learn/challenges)