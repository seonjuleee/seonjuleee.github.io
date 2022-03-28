---
title: "[프로그래머스] 베스트 앨범"
date: 2021-05-15
update: 2021-05-15
tags:
  - Algorithm
  - Programmers
---

> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/42579>

### 문제 설명

스트리밍 사이트에서 장르 별로 가장 많이 재생된 노래를 두 개씩 모아 베스트 앨범을 출시하려 합니다. 노래는 고유 번호로 구분하며, 노래를 수록하는 기준은 다음과 같습니다.

속한 노래가 많이 재생된 장르를 먼저 수록합니다.
장르 내에서 많이 재생된 노래를 먼저 수록합니다.
장르 내에서 재생 횟수가 같은 노래 중에서는 고유 번호가 낮은 노래를 먼저 수록합니다.
노래의 장르를 나타내는 문자열 배열 genres와 노래별 재생 횟수를 나타내는 정수 배열 plays가 주어질 때, 베스트 앨범에 들어갈 노래의 고유 번호를 순서대로 return 하도록 solution 함수를 완성하세요.

### 제한사항

- genres[i]는 고유번호가 i인 노래의 장르입니다.
- plays[i]는 고유번호가 i인 노래가 재생된 횟수입니다.
- genres와 plays의 길이는 같으며, 이는 1 이상 10,000 이하입니다.
- 장르 종류는 100개 미만입니다.
- 장르에 속한 곡이 하나라면, 하나의 곡만 선택합니다.
- 모든 장르는 재생된 횟수가 다릅니다.

### 입출력 예

|genres|plays|return|
|-|-|-|
|["classic", "pop", "classic", "classic", "pop"]|[500, 600, 150, 800, 2500]|[4, 1, 3, 0]|

classic 장르는 1,450회 재생되었으며, classic 노래는 다음과 같습니다.

- 고유 번호 3: 800회 재생
- 고유 번호 0: 500회 재생
- 고유 번호 2: 150회 재생

pop 장르는 3,100회 재생되었으며, pop 노래는 다음과 같습니다.

- 고유 번호 4: 2,500회 재생
- 고유 번호 1: 600회 재생

따라서 pop 장르의 [4, 1]번 노래를 먼저, classic 장르의 [3, 0]번 노래를 그다음에 수록합니다.

### 내 풀이

연관되어 있는 데이터가 장르, 재생수, 인덱스 이렇게 세 개이므로 객체를 만들어준다.

```java
public class Music {
    String genre;
    int play;
    int index;

    public Music(String genre, int play, int index) {
        this.genre = genre;
        this.play = play;
        this.index = index;
    }
}
```

각 배열을 돌면서 map에 장르와 장르 당 총 재생 수를 저장한다. list는 각 객체를 저장한 리스트이다.

```java
Map<String, Integer> map = new HashMap<>();
List<Music> list = new ArrayList<>();
for (int i=0; i<genres.length; i++) {
    list.add(new Music(genres[i], plays[i], i));
    if (map.containsKey(genres[i])) {
        map.put(genres[i], map.get(genres[i]) + plays[i]);
    } else {
        map.put(genres[i], plays[i]);
    }
}
```

map에서 총 재생수로 내림차순 정렬을 시켜 많이 재생된 장르를 먼저 수록하도록 한다.
list도 재생수 내림차순 정렬하여 노래가 재생 수대로 정렬되도록 한다.(이때 장르는 상관없이 내림차순 됨)

```java
List<String> keySet = new ArrayList<>(map.keySet());
keySet.sort(((o1, o2) -> map.get(o2) - map.get(o1)));

Collections.sort(list, new Comparator<Music>() {
    @Override
    public int compare(Music o1, Music o2) {
        return o2.play - o1.play;
    }
});
```

map을 돌면서 가장 재생수의 합이 많은 장르대로 list에서 가장 높은 재생수를 가지는 두 곡의 인덱스를 answer 배열에 담는다.

```java
List<Integer> ans = new ArrayList<>();
for (String key : keySet) {
int count = 0;
for (Music m : list) {
    if (key.equals(m.genre)) {
        ans.add(m.index);
        count++;
        if (count == 2) break;
    }
}
}
```

### 전체 코드

```java
import java.util.*;
class Solution {
    public class Music {
        String genre;
        int play;
        int index;

        public Music(String genre, int play, int index) {
            this.genre = genre;
            this.play = play;
            this.index = index;
        }
    }
    
    public int[] solution(String[] genres, int[] plays) {
        Map<String, Integer> map = new HashMap<>();
        List<Music> list = new ArrayList<>();
        for (int i=0; i<genres.length; i++) {
            list.add(new Music(genres[i], plays[i], i));
            if (map.containsKey(genres[i])) {
                map.put(genres[i], map.get(genres[i]) + plays[i]);
            } else {
                map.put(genres[i], plays[i]);
            }
        }
        List<Integer> ans = new ArrayList<>();
        List<String> keySet = new ArrayList<>(map.keySet());
        keySet.sort(((o1, o2) -> map.get(o2) - map.get(o1)));

        Collections.sort(list, new Comparator<Music>() {
            @Override
            public int compare(Music o1, Music o2) {
                return o2.play - o1.play;
            }
        });

        for (String key : keySet) {
            int count = 0;
            for (Music m : list) {
                if (key.equals(m.genre)) {
                    ans.add(m.index);
                    count++;
                    if (count == 2) break;
                }
            }
        }

        int[] answer = new int[ans.size()];

        for (int i=0; i<ans.size(); i++) {
            answer[i] = ans.get(i);
        }
        
        return answer;
    }
}
```