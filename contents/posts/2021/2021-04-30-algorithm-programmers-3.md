---
title: "[프로그래머스] 네트워크"
date: 2021-04-30
update: 2021-04-30
tags:
  - Algorithm
  - Programmers
---

> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/43162>

### 문제 설명

네트워크란 컴퓨터 상호 간에 정보를 교환할 수 있도록 연결된 형태를 의미합니다. 예를 들어, 컴퓨터 A와 컴퓨터 B가 직접적으로 연결되어있고, 컴퓨터 B와 컴퓨터 C가 직접적으로 연결되어 있을 때 컴퓨터 A와 컴퓨터 C도 간접적으로 연결되어 정보를 교환할 수 있습니다. 따라서 컴퓨터 A, B, C는 모두 같은 네트워크 상에 있다고 할 수 있습니다.

컴퓨터의 개수 n, 연결에 대한 정보가 담긴 2차원 배열 computers가 매개변수로 주어질 때, 네트워크의 개수를 return 하도록 solution 함수를 작성하시오.

### 제한사항

- 컴퓨터의 개수 n은 1 이상 200 이하인 자연수입니다.
- 각 컴퓨터는 0부터 n-1인 정수로 표현합니다.
- i번 컴퓨터와 j번 컴퓨터가 연결되어 있으면 computers[i][j]를 1로 표현합니다.
- computer[i][i]는 항상 1입니다.

### 입출력 예

|n|computers|return|
|:---|:---|:---|
|3|[[1, 1, 0], [1, 1, 1], [0, 1, 1]]|2|
|3|[[1, 1, 0], [1, 1, 1], [0, 1, 1]]|1|

### 내 풀이

dfs로 돌면서 같은 네트워크에 있는 컴퓨터들을 검사하고, dfs 메소드를 실행할 때마다 네트워크 개수가 늘어나는 것이기 때문에 `answer++`을 한다. 
컴퓨터들을 검사하기 전에 이전에 방문하지 않은 컴퓨터들만 검사해야하기 때문에 isVisit 배열을 통해 방문 여부를 저장하고, 방문하지 않은(`!isVisit[i]`) 컴퓨터들만 검사하도록 한다.

```java
class Solution {
    public static boolean[] isVisit;
    public int solution(int n, int[][] computers) {
        int answer = 0;
        isVisit = new boolean[n];
        
        for (int i=0; i<n; i++) {
            if (!isVisit[i]) {
                isVisit[i] = true;
                answer++;
                dfs(i, n, computers);
            }
        }
        return answer;
    }
    public void dfs(int j, int n, int[][] coms) {
        for (int k=0; k<n; k++) {
            if (!isVisit[k] && coms[j][k] == 1) {
                isVisit[k] = true;
                dfs(k, n, coms);
            }
        }
    }
}
```