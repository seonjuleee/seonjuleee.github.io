---
layout: post
title: "[프로그래머스] 가장 큰 수"
categories: [Algorithm]
---

> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/42746>

### 문제 설명

0 또는 양의 정수가 주어졌을 때, 정수를 이어 붙여 만들 수 있는 가장 큰 수를 알아내 주세요.

예를 들어, 주어진 정수가 [6, 10, 2]라면 [6102, 6210, 1062, 1026, 2610, 2106]를 만들 수 있고, 이중 가장 큰 수는 6210입니다.

0 또는 양의 정수가 담긴 배열 numbers가 매개변수로 주어질 때, 순서를 재배치하여 만들 수 있는 가장 큰 수를 문자열로 바꾸어 return 하도록 solution 함수를 작성해주세요.

### 제한 사항

- numbers의 길이는 1 이상 100,000 이하입니다.
- numbers의 원소는 0 이상 1,000 이하입니다.
- 정답이 너무 클 수 있으니 문자열로 바꾸어 return 합니다.

### 입출력 예

|numbers|return|
|-|-|
|[6, 10, 12]|"6210"|
|[3, 30, 34, 5, 9]|"9534330"|

### 내 풀이

Collections의 sort 메소드를 사용하기 위해 배열을 list에 담아 sort 메소드를 Comparator을 이용해 새로 구성한다.

```java
Collections.sort(list, new Comparator<String>() {
    @Override
    public int compare(String o1, String o2) {
        return Integer.parseInt(o2 + o1) - Integer.parseInt(o1 + o2);
    }
});
```

마지막 테스트 케이스만 실패를 했는데, 이유는 numbers의 모든 원소가 0일 때 0을 출력한다는 점을 고려하지 않아 발생한 문제였다. 반례를 생각하는데 시간이 오래 걸린 문제였다.

```java
if (list.get(0).equals("0")) return "0";
```

### 전체 코드

```java
import java.util.*;
class Solution {
    public String solution(int[] numbers) {
        StringBuilder sb = new StringBuilder();
        List<String> list = new ArrayList<>();
        for (int n : numbers) {
            list.add(String.valueOf(n));
        }
        Collections.sort(list, new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return Integer.parseInt(o2 + o1) - Integer.parseInt(o1 + o2);
            }
        });
        if (list.get(0).equals("0")) return "0";
        for (int i=0; i<list.size(); i++) {
            sb.append(list.get(i));
        }
        return sb.toString();
    }
}
```