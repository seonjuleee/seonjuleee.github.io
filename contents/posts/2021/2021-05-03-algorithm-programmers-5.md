---
title: "[프로그래머스] 여행경로"
date: 2021-05-03
update: 2021-05-03
tags:
  - Algorithm
  - Programmers
---

> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/43164>

### 문제 설명

주어진 항공권을 모두 이용하여 여행경로를 짜려고 합니다. 항상 "ICN" 공항에서 출발합니다.

항공권 정보가 담긴 2차원 배열 tickets가 매개변수로 주어질 때, 방문하는 공항 경로를 배열에 담아 return 하도록 solution 함수를 작성해주세요.

### 제한사항

- 모든 공항은 알파벳 대문자 3글자로 이루어집니다.
- 주어진 공항 수는 3개 이상 10,000개 이하입니다.
- tickets의 각 행 [a, b]는 a 공항에서 b 공항으로 가는 항공권이 있다는 의미입니다.
- 주어진 항공권은 모두 사용해야 합니다.
- 만일 가능한 경로가 2개 이상일 경우 알파벳 순서가 앞서는 경로를 return 합니다.
- 모든 도시를 방문할 수 없는 경우는 주어지지 않습니다.

### 입출력 예

|tickets|return|
|-|-|
|[["ICN", "JFK"], ["HND", "IAD"], ["JFK", "HND"]]|["ICN", "JFK", "HND", "IAD"]|
|[["ICN", "SFO"], ["ICN", "ATL"], ["SFO", "ATL"], ["ATL", "ICN"], ["ATL","SFO"]]|["ICN", "ATL", "ICN", "SFO", "ATL", "SFO"]|

### 내 풀이

1) dfs 함수

주어진 항공권을 모두 사용해야 하므로 항상 경로에 있는 공항의 수가 티켓의 수보다 1개 더 많아야 하기 때문에 리턴 조건을 `route.size() == tickets.length + 1`로 설정한다. 그리고 가능한 경로가 2개 이상일 경우, 조건에 의해 알파벳 순서가 앞서는 경로를 return 해야하므로 compare 함수를 통해 비교할 수 있다.

주어진 항공권을 다 사용하지 않은 경우, 아직 사용하지 않았으며 다음 경로로 갈 수 있는 항공권을 선택해 dfs를 다시 돌아야한다. 다음 경로로 갈 수 있는 항공권이 여러 개 있을 수 있기 때문에 dfs가 끝나면 경로에서 빼고 갈 수 있는 다른 경로를 찾을 수 있도록 한다.

```java
public void dfs(String[][] tickets, ArrayList<String> route, boolean[] chk) {
    if (route.size() == tickets.length + 1) {
        if (answer.length <= 0 || compare(route, answer)) { // route가 answer보다 사전순으로 앞에 있으면
            answer = route.toArray(new String[0]);
        }
        return;
    }
    // dfs
    for (int i=0; i< tickets.length; i++) {
        if (!chk[i] && route.get(route.size() - 1).equals(tickets[i][0])) {
            chk[i] = true;
            route.add(tickets[i][1]);
            dfs(tickets, route, chk);
            route.remove(route.size() - 1);
            chk[i] = false;
        }
    }
}
```

2) compare 함수

앞에 있는 배열이 사전순으로 앞에 위치한 경우, true를 리턴, 그렇지 않으면 false를 리턴하도록 한다.

```java
public boolean compare(ArrayList<String> a, String[] b) {
    for (int i = 0; i < a.size(); i++) {
        if (a.get(i).compareTo(b[i]) < 0) { // a가 사전순으로 앞에 있으면
            return true;
        } else if (a.get(i).compareTo(b[i]) > 0) { // a가 사전순으로 뒤에 있으면
            return false;
        }
    }
    return false;
}
```

### 전체 코드
```java
import java.util.ArrayList;
class Solution {
    static String[] answer;
    public String[] solution(String[][] tickets) {
        answer = new String[]{};
        ArrayList<String> route = new ArrayList<>();
        route.add("ICN");
        dfs(tickets, route, new boolean[tickets.length]);
        return answer;
    }
    public void dfs(String[][] tickets, ArrayList<String> route, boolean[] chk) {
        if (route.size() == tickets.length + 1) {
            if (answer.length <= 0 || compare(route, answer)) { // route가 answer보다 사전순으로 앞에 있으면
                answer = route.toArray(new String[0]);
            }
            return;
        }
        // dfs
        for (int i=0; i< tickets.length; i++) {
            if (!chk[i] && route.get(route.size() - 1).equals(tickets[i][0])) {
                chk[i] = true;
                route.add(tickets[i][1]);
                dfs(tickets, route, chk);
                route.remove(route.size() - 1);
                chk[i] = false;
            }
        }
    }
    public boolean compare(ArrayList<String> a, String[] b) {
        for (int i = 0; i < a.size(); i++) {
            if (a.get(i).compareTo(b[i]) < 0) { // a가 사전순으로 앞에 있으면
                return true;
            } else if (a.get(i).compareTo(b[i]) > 0) { // a가 사전순으로 뒤에 있으면
                return false;
            }
        }
        return false;
    }
}

```

테스트 케이스 한 개가 통과를 못해서 시간이 오래 걸린 문제였다. 중복 배열을 생각하지 못해서 반례를 찾기 힘들었고, 결국 질문 페이지에 있는 반례를 가지고 문제를 해결할 수 있었다.