---
layout: post
title: "[프로그래머스] 카펫"
categories: [Algorithm]
---

> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/42842>

### 문제 설명

Leo는 카펫을 사러 갔다가 아래 그림과 같이 중앙에는 노란색으로 칠해져 있고 테두리 1줄은 갈색으로 칠해져 있는 격자 모양 카펫을 봤습니다.

![문제 설명]({{"/assets/images/algorithm-programmers-21.png"| relative_url}})

Leo는 집으로 돌아와서 아까 본 카펫의 노란색과 갈색으로 색칠된 격자의 개수는 기억했지만, 전체 카펫의 크기는 기억하지 못했습니다.

Leo가 본 카펫에서 갈색 격자의 수 brown, 노란색 격자의 수 yellow가 매개변수로 주어질 때 카펫의 가로, 세로 크기를 순서대로 배열에 담아 return 하도록 solution 함수를 작성해주세요.

### 제한사항

- 갈색 격자의 수 brown은 8 이상 5,000 이하인 자연수입니다.
- 노란색 격자의 수 yellow는 1 이상 2,000,000 이하인 자연수입니다.
- 카펫의 가로 길이는 세로 길이와 같거나, 세로 길이보다 깁니다.

### 입출력 예

|brown|yellow|return|
|-|-|-|
|10|2|[4, 3]|
|8|1|[3, 3]|

### 내 풀이

`brown + yellow`의 값이 전체 격자의 개수와 같기 때문에 `n`으로 두고, 가로의 개수인 `i`를 3부터 올리면서 n의 약수인지 체크하고, 그 때의 `yellow`와 같은지 확인 후 맞으면 리턴한다.

```java
class Solution {
    public int[] solution(int brown, int yellow) {
        int n = brown + yellow;
        for (int i=3; n/i>=3; i++) {
            if (n % i == 0 && yellow == (i - 2) * (n/i - 2)) {
                int[] answer = {(n / i), i};
                return answer;
            }
        }
        return new int[]{};
    }
}
```
