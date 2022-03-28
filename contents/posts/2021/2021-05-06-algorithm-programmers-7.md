---
title: "[프로그래머스] 프린터"
date: 2021-05-06
update: 2021-05-06
tags:
  - Algorithm
  - Programmers
---

> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/42587>

### 문제 설명

일반적인 프린터는 인쇄 요청이 들어온 순서대로 인쇄합니다. 그렇기 때문에 중요한 문서가 나중에 인쇄될 수 있습니다. 이런 문제를 보완하기 위해 중요도가 높은 문서를 먼저 인쇄하는 프린터를 개발했습니다. 이 새롭게 개발한 프린터는 아래와 같은 방식으로 인쇄 작업을 수행합니다.

```
1. 인쇄 대기목록의 가장 앞에 있는 문서(J)를 대기목록에서 꺼냅니다.
2. 나머지 인쇄 대기목록에서 J보다 중요도가 높은 문서가 한 개라도 존재하면 J를 대기목록의 가장 마지막에 넣습니다.
3. 그렇지 않으면 J를 인쇄합니다.
```

예를 들어, 4개의 문서(A, B, C, D)가 순서대로 인쇄 대기목록에 있고 중요도가 2 1 3 2 라면 C D A B 순으로 인쇄하게 됩니다.

내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 알고 싶습니다. 위의 예에서 C는 1번째로, A는 3번째로 인쇄됩니다.

현재 대기목록에 있는 문서의 중요도가 순서대로 담긴 배열 priorities와 내가 인쇄를 요청한 문서가 현재 대기목록의 어떤 위치에 있는지를 알려주는 location이 매개변수로 주어질 때, 내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 return 하도록 solution 함수를 작성해주세요.

### 제한사항

- 현재 대기목록에는 1개 이상 100개 이하의 문서가 있습니다.
- 인쇄 작업의 중요도는 1~9로 표현하며 숫자가 클수록 중요하다는 뜻입니다.
- location은 0 이상 (현재 대기목록에 있는 작업 수 - 1) 이하의 값을 가지며 대기목록의 가장 앞에 있으면 0, 두 번째에 있으면 1로 표현합니다.

### 입출력 예

|priorities|location|return|
|-|-|-|
|[2, 1, 3, 2]|2|1|
|[1, 1, 9, 1, 1]|0|5|

예제 #1
문제에 나온 예와 같습니다.

예제 #2
6개의 문서(A, B, C, D, E, F)가 인쇄 대기목록에 있고 중요도가 1 1 9 1 1 1 이므로 C D E F A B 순으로 인쇄합니다.

### 내 풀이

앞이나 뒤에 있는 원소들을 추가, 삭제를 하거나 인덱스에 있는 원소를 얻기 위해서 List를 이용했다. 문제에서 내가 인쇄를 요청한 문서의 인덱스를 얻어야 하는데, 리스트에 넣고 빼면 고유한 인덱스가 사라지기 때문에 인덱스를 따로 저장하기 위해서 Pair 객체를 만들어서 index와 value를 저장했다.

다른 사람들의 풀이를 보니 이런 방식보다는 location의 값을 조정하던데 그 방법이 더 간결하고 적합해보였다. 다음에는 이 방식으로 다시 풀어봐야겠다.

List에 저장된 객체 중 가장 큰 수를 찾고, for문을 돌면서 최댓값이 나올 때까지 값을 뒤로 보낸다. 최댓값을 찾았을 때, 그 인덱스가 location과 같으면 그 때의 answer값이 답이므로 리턴한다.

```java
import java.util.*;
class Solution {
    static class Pair {
        int index, value;
        
        public Pair(int index, int value) {
            this.index = index;
            this.value = value;
        }
    }
    
    public int solution(int[] priorities, int location) {
        int answer = 0;
        List<Pair> list = new ArrayList<Pair>();
        for (int i=0; i<priorities.length; i++) {
            list.add(new Pair(i, priorities[i]));
        }
        while (!list.isEmpty()) {
            int max = 0;
            for (int i=0; i<list.size(); i++) {
                max = list.get(i).value > max ? list.get(i).value : max;
            }
            for (int i=0; i<list.size(); i++) {
                Pair p = list.remove(0);
                if (max != p.value) list.add(p);
                else {
                    answer++;
                    if (p.index == location) return answer;
                    break;
                }
            }
        }
        return answer;
    }
}
```