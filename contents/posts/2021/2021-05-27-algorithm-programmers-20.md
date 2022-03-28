---
title: "[프로그래머스] 소수 찾기"
date: 2021-05-27
update: 2021-05-27
tags:
  - Algorithm
  - Programmers
---


> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/42839>

### 문제 설명

한자리 숫자가 적힌 종이 조각이 흩어져있습니다. 흩어진 종이 조각을 붙여 소수를 몇 개 만들 수 있는지 알아내려 합니다.

각 종이 조각에 적힌 숫자가 적힌 문자열 numbers가 주어졌을 때, 종이 조각으로 만들 수 있는 소수가 몇 개인지 return 하도록 solution 함수를 완성해주세요.

### 제한사항

- numbers는 길이 1 이상 7 이하인 문자열입니다.
- numbers는 0~9까지 숫자만으로 이루어져 있습니다.
- "013"은 0, 1, 3 숫자가 적힌 종이 조각이 흩어져있다는 의미입니다.

### 입출력 예

|numbers|return|
|-|-|
|"17"|3|
|"011"|2|

[1, 7]으로는 소수 [7, 17, 71]를 만들 수 있습니다.
[0, 1, 1]으로는 소수 [11, 101]를 만들 수 있습니다.
- 11과 011은 같은 숫자로 취급합니다.

### 내 풀이

먼저 문자열의 길이가 최대 7이하이므로 문자열로 만들어질 수 있는 최대 수는 `10000000`이므로, 1억까지의 수를 각각 소수인지 아닌지 표시한다.

소수 배열을 boolean이 아닌 int 타입으로 설정한 이유는 나중에 같은 수의 중복을 막기 위해서다. 순서에 따라 경우의 수가 달라지지만 예제 2번에서 11인 경우 앞뒤가 한 번 바뀌어서 11인 경우도 같은 경우로 취급해줘야 하기 때문에 `np` 배열에 소수를 세었는지 안세었는지도 표시하기 위함이다.

```java
// 소수 표시, 소수면 0, 소수가 아니면 -1
np = new int[10000001];
for (int i=2; i<=10000000; i++) {
    if (np[i] == -1) continue;
    for (int j=i*2; j<=10000000; j+=i) {
        np[j] = -1;
    }
}
np[0] = np[1] = -1;
```

이제 조각을 골라야 하는데, 순서가 상관이 있기 때문에 순열을 사용한다.

`str`은 주어진 `numbers`, `out`은 지금 고른 조각들, `check`은 방문 여부를 체크하는 배열, `depth`는 몇 개 골랐는 지를 나타내는 변수이다.

처음에는 `out`이 비어있다는 의미로 `""`가 들어가는데, 이걸 예외 처리 해주지 않으면 `np[Integer.valueOf(out)]` 이 부분에서 오류가 생긴다.

```java
public static void perm(String str, String out, boolean[] check, int depth, int n) {
    if (!out.equals("") && np[Integer.valueOf(out)] == 0) {
        np[Integer.valueOf(out)]++; // 0이면 1로 만들어서 중복 막기
        answer++;
    }

    for (int i=0; i<n; i++) {
        if (!check[i]) {
            check[i] = true;
            perm(str, out + str.charAt(i), check, depth + 1, n);
            check[i] = false;
        }
    }
}
```

### 전체 코드

```java
class Solution {
    static int[] np;
    static int answer;
    
    public int solution(String numbers) {
        answer = 0;

        // 소수 표시, 소수면 0, 소수가 아니면 -1
        np = new int[10000001];
        for (int i=2; i<=10000000; i++) {
            if (np[i] == -1) continue;
            for (int j=i*2; j<=10000000; j+=i) {
                np[j] = -1;
            }
        }
        np[0] = np[1] = -1;

        // 조각 고르기 -> 순열
        boolean[] chk = new boolean[numbers.length()];
        perm(numbers, "", chk, 0, numbers.length());

        return answer;
    }
    public static void perm(String str, String out, boolean[] check, int depth, int n) {
        if (!out.equals("") && np[Integer.valueOf(out)] == 0) {
            np[Integer.valueOf(out)]++; // 0이면 1로 만들어서 중복 막기
            answer++;
        }

        for (int i=0; i<n; i++) {
            if (!check[i]) {
                check[i] = true;
                perm(str, out + str.charAt(i), check, depth + 1, n);
                check[i] = false;
            }
        }
    }
}
```