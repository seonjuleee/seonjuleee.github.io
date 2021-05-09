---
layout: post
title: "[프로그래머스] 단어 변환"
categories: [Algorithm]
---

> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/43163>

### 문제 설명

두 개의 단어 begin, target과 단어의 집합 words가 있습니다. 아래와 같은 규칙을 이용하여 begin에서 target으로 변환하는 가장 짧은 변환 과정을 찾으려고 합니다.

1. 한 번에 한 개의 알파벳만 바꿀 수 있습니다.
2. words에 있는 단어로만 변환할 수 있습니다.

예를 들어 begin이 "hit", target가 "cog", words가 ["hot","dot","dog","lot","log","cog"]라면 "hit" -> "hot" -> "dot" -> "dog" -> "cog"와 같이 4단계를 거쳐 변환할 수 있습니다.

두 개의 단어 begin, target과 단어의 집합 words가 매개변수로 주어질 때, 최소 몇 단계의 과정을 거쳐 begin을 target으로 변환할 수 있는지 return 하도록 solution 함수를 작성해주세요.

### 제한사항

- 각 단어는 알파벳 소문자로만 이루어져 있습니다.
- 각 단어의 길이는 3 이상 10 이하이며 모든 단어의 길이는 같습니다.
- words에는 3개 이상 50개 이하의 단어가 있으며 중복되는 단어는 없습니다.
- begin과 target은 같지 않습니다.
- 변환할 수 없는 경우에는 0를 return 합니다.

### 입출력 예

|begin|target|words|return|
|-|-|-|-|
|"hit"|"cog"|["hot", "dot", "dog", "lot", "log", "cog"]|4|
|"hit"|"cog"|["hot", "dot", "dog", "lot", "log"]|0|

### 내 풀이

1. words 배열에 target이 없다면 바로 0 리턴
2. i번째 문자를 뺀 나머지 문자가 동일한지 words 배열을 검사
3. 나머지 문자가 동일하면 방문 처리(`chk[j] = true`) 후 다음 단계의 글자를 찾음
4. 탐색 중 target과 동일한 문자가 나왔을 때 answer 처리(최솟값)

answer 처리 부분이 잘 안되어서 맞춰가며 코드를 수정하다보니 코드가 조금 번잡해진게 있는 것 같다. 조금 더 깔끔하게 코드를 작성할 수 있도록 연습해야겠다.

```java
class Solution {
    static int answer;
    public int solution(String begin, String target, String[] words) {
        answer = 0;
        boolean[] chk = new boolean[words.length];

        // words에 target이 있는지 확인
        int flag = 0;
        for (String w : words) {
            if(w.equals(target)) {
                flag = 1;
                break;
            }
        }
        if (flag == 0) return 0;
        int ans = 0;
        dfs(begin, chk, target, words, ans);
        return answer;
    }
    
    public void dfs(String str, boolean[] chk, String target, String[] words, int ans) {
        if (str.equals(target)) {
            answer = answer == 0 ? ans : Math.min(ans, answer);
            return;
        }
        for (int i=0; i<str.length(); i++) {
            for (int j=0; j<words.length; j++) {
                if(!chk[j]) {
                    String ls = (i >= 1) ? str.substring(0, i) : "";
                    String rs = (i < str.length() - 1) ? str.substring(i + 1) : "";
                    String lw = (i >= 1) ? words[j].substring(0, i) : "";
                    String rw = (i < str.length() - 1) ? words[j].substring(i + 1) : "";
                    if (ls.equals(lw) && rs.equals(rw)) {
                        chk[j] = true;
                        ans++;
                        dfs(words[j], chk, target, words, ans);
                        ans--;
                        chk[j] = false;
                    }
                }
            }
        }
    }
}
```