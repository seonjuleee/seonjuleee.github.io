---
layout: post
title: "[프로그래머스] 위장"
categories: [Algorithm]
---

> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/42578>

### 문제 설명

스파이들은 매일 다른 옷을 조합하여 입어 자신을 위장합니다.

예를 들어 스파이가 가진 옷이 아래와 같고 오늘 스파이가 동그란 안경, 긴 코트, 파란색 티셔츠를 입었다면 다음날은 청바지를 추가로 입거나 동그란 안경 대신 검정 선글라스를 착용하거나 해야 합니다.

|종류|이름|
|-|-|
|얼굴|동그란 안경, 검정 선글라스|
|상의|파란색 티셔츠|
|하의|청바지|
|겉옷|긴 코트|

스파이가 가진 의상들이 담긴 2차원 배열 clothes가 주어질 때 서로 다른 옷의 조합의 수를 return 하도록 solution 함수를 작성해주세요.

### 제한사항

- clothes의 각 행은 [의상의 이름, 의상의 종류]로 이루어져 있습니다.
- 스파이가 가진 의상의 수는 1개 이상 30개 이하입니다.
- 같은 이름을 가진 의상은 존재하지 않습니다.
- clothes의 모든 원소는 문자열로 이루어져 있습니다.
- 모든 문자열의 길이는 1 이상 20 이하인 자연수이고 알파벳 소문자 또는 '_' 로만 이루어져 있습니다.
- 스파이는 하루에 최소 한 개의 의상은 입습니다.

### 입출력 예

|clothes|return|
|-|-|
|[["yellowhat", "headgear"], ["bluesunglasses", "eyewear"], ["green_turban", "headgear"]]|5|
|[["crowmask", "face"], ["bluesunglasses", "face"], ["smoky_makeup", "face"]]|3|

예제 #1

headgear에 해당하는 의상이 yellow_hat, green_turban이고 eyewear에 해당하는 의상이 blue_sunglasses이므로 아래와 같이 5개의 조합이 가능합니다.

```
1. yellow_hat
2. blue_sunglasses
3. green_turban
4. yellow_hat + blue_sunglasses
5. green_turban + blue_sunglasses
```

예제 #2

face에 해당하는 의상이 crow_mask, blue_sunglasses, smoky_makeup이므로 아래와 같이 3개의 조합이 가능합니다.

```
1. crow_mask
2. blue_sunglasses
3. smoky_makeup
```

### 내 풀이

먼저 같은 종류인 것의 수를 세기 위해 종류가 key인 Map을 만들었다. Map에 있는 원소는 value에 1을 더하고, Map에 없는 원소는 value를 1로 해서 Map에 넣어준다.

이후 종류의 이름은 상관없이 각각이 몇 개인지만 알면 되는데, 이후 과정에서 인덱스가 필요해서 List에 따로 저장을 했다.

```java
Map<String, Integer> map = new HashMap<>();
for (int i=0; i<clothes.length; i++) {
    if (map.containsKey(clothes[i][1])) {
        map.replace(clothes[i][1], map.get(clothes[i][1]) + 1);
    } else {
        map.put(clothes[i][1], 1);
    }
}
list = new ArrayList<>();
map.forEach((k, v) -> list.add(v));
```

이제 서로 다른 옷의 조합의 수를 세야 하는데, 예를 들어 list가 `[2, 1, 1, 1]` 이렇게 주어졌다면 각각이 입거나 안입거나를 선택할 수 있는데, 입는 경우의 수는 원소의 value 값이다.

따라서 dfs를 이용해서 입는 경우와 안입는 경우를 선택해 탐색하는 형식으로 구현하였다.

```java
public void dfs(int i, int result) {
    if (i >= list.size()) {
        answer += result;
        return;
    }
    dfs(i + 1, result * list.get(i));
    dfs(i + 1, result);
}
```

문제의 조건에서 스파이는 하루에 최소 한 개의 의상은 입기 때문에 아무것도 입지 않는 경우는 빼야한다. 그래서 solution 함수 마지막에 `return answer - 1;`로 해줘야 한다.

### 전체 코드

```java
import java.util.*;
class Solution {
    static List<Integer> list;
    static int answer = 0;
    
    public int solution(String[][] clothes) {
        Map<String, Integer> map = new HashMap<>();
        for (int i=0; i<clothes.length; i++) {
            if (map.containsKey(clothes[i][1])) {
                map.replace(clothes[i][1], map.get(clothes[i][1]) + 1);
            } else {
                map.put(clothes[i][1], 1);
            }
        }
        list = new ArrayList<>();
        map.forEach((k, v) -> list.add(v));
        dfs(0, 1);
        return answer - 1;
    }
    public void dfs(int i, int result) {
        if (i >= list.size()) {
            answer += result;
            return;
        }
        dfs(i + 1, result * list.get(i));
        dfs(i + 1, result);
    }
}
```

### 다른 사람의 풀이

생각보다 더 간단하게 풀 수 있는 방법이 있었다. 

dfs 과정을 거치지 말고, List의 원소의 value가 N이면 안입거나, 1개 입거나, 2개 입거나, ..., N개 입거나를 선택하는 것이기 때문에 그 경우의 수는 N + 1이고, List 원소 각각을 이런 식으로 경우의 수를 구해 모두를 곱해주면 되는 방법이다.

앞서 들은 예제로, value가 각각 `[2, 1, 1, 1]` 이렇게 있다면 간단하게 `3 * 2 * 2 * 2`로 계산하면 된다.

```java
import java.util.*;
class Solution {
    public int solution(String[][] clothes) {
        int answer = 1;
        Map<String, Integer> map = new HashMap<>();
        for (int i=0; i<clothes.length; i++) {
            if (map.containsKey(clothes[i][1])) {
                map.replace(clothes[i][1],map.get(clothes[i][1]) + 1);
            } else {
                map.put(clothes[i][1], 1);
            }
        }
        for (String k : map.keySet()) {
            answer *= map.get(k) + 1;
        }
        return answer - 1;
    }
}
```