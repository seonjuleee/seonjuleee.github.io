---
title: "[프로그래머스] 디스크 컨트롤러"
date: 2021-05-18
update: 2021-05-18
tags:
  - Algorithm
  - Programmers
---

> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/42627>

### 문제 설명

하드디스크는 한 번에 하나의 작업만 수행할 수 있습니다. 디스크 컨트롤러를 구현하는 방법은 여러 가지가 있습니다. 가장 일반적인 방법은 요청이 들어온 순서대로 처리하는 것입니다.

예를들어
```
- 0ms 시점에 3ms가 소요되는 A작업 요청
- 1ms 시점에 9ms가 소요되는 B작업 요청
- 2ms 시점에 6ms가 소요되는 C작업 요청
```
와 같은 요청이 들어왔습니다. 이를 그림으로 표현하면 아래와 같습니다.

![문제 설명1]({{"/assets/images/algorithm-programmers-14-1.png"| relative_url}})

한 번에 하나의 요청만을 수행할 수 있기 때문에 각각의 작업을 요청받은 순서대로 처리하면 다음과 같이 처리 됩니다.

![문제 설명2]({{"/assets/images/algorithm-programmers-14-2.png"| relative_url}})

```
- A: 3ms 시점에 작업 완료 (요청에서 종료까지 : 3ms)
- B: 1ms부터 대기하다가, 3ms 시점에 작업을 시작해서 12ms 시점에 작업 완료(요청에서 종료까지 : 11ms)
- C: 2ms부터 대기하다가, 12ms 시점에 작업을 시작해서 18ms 시점에 작업 완료(요청에서 종료까지 : 16ms)
```

이 때 각 작업의 요청부터 종료까지 걸린 시간의 평균은 10ms(= (3 + 11 + 16) / 3)가 됩니다.

하지만 A → C → B 순서대로 처리하면

![문제 설명3]({{"/assets/images/algorithm-programmers-14-3.png"| relative_url}})

```
- A: 3ms 시점에 작업 완료(요청에서 종료까지 : 3ms)
- C: 2ms부터 대기하다가, 3ms 시점에 작업을 시작해서 9ms 시점에 작업 완료(요청에서 종료까지 : 7ms)
- B: 1ms부터 대기하다가, 9ms 시점에 작업을 시작해서 18ms 시점에 작업 완료(요청에서 종료까지 : 17ms)
이렇게 A → C → B의 순서로 처리하면 각 작업의 요청부터 종료까지 걸린 시간의 평균은 9ms(= (3 + 7 + 17) / 3)가 됩니다.
```

각 작업에 대해 [작업이 요청되는 시점, 작업의 소요시간]을 담은 2차원 배열 jobs가 매개변수로 주어질 때, 작업의 요청부터 종료까지 걸린 시간의 평균을 가장 줄이는 방법으로 처리하면 평균이 얼마가 되는지 return 하도록 solution 함수를 작성해주세요. (단, 소수점 이하의 수는 버립니다)

### 제한 사항
- jobs의 길이는 1 이상 500 이하입니다.
- jobs의 각 행은 하나의 작업에 대한 [작업이 요청되는 시점, 작업의 소요시간] 입니다.
- 각 작업에 대해 작업이 요청되는 시간은 0 이상 1,000 이하입니다.
- 각 작업에 대해 작업의 소요시간은 1 이상 1,000 이하입니다.
- 하드디스크가 작업을 수행하고 있지 않을 때에는 먼저 요청이 들어온 작업부터 처리합니다.

### 입출력 예

|jobs|return|
|-|-|
|[[0, 3], [1, 9], [2, 6]]|9|

- 0ms 시점에 3ms 걸리는 작업 요청이 들어옵니다.
- 1ms 시점에 9ms 걸리는 작업 요청이 들어옵니다.
- 2ms 시점에 6ms 걸리는 작업 요청이 들어옵니다.

### 내 풀이

우선순위 큐를 이용해 큐에 작업을 넣었다가 빼면서 시간을 계산한다는 것을 토대로 문제를 풀었지만, 큐나 리스트를 작업과 대기 리스트로 나누어서 해야 된다는 점을 간과해 문제를 풀지 못했다.

이 <b>[포스트](https://velog.io/@hyeon930/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4-%EB%94%94%EC%8A%A4%ED%81%AC-%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC-Java)</b>를 참고해 문제를 다시 풀었다.

1. 대기 큐에서 요청되는 시점이 현재 시간보다 적은 경우에 작업 큐로 옮긴다.
2. 작업 큐에서 소요 시간이 적은 순대로 꺼내 작업한다.
3. 작업 과정
    - 현재 시간에 작업이 끝나는 시간을 더한다. (작업이 종료되는 시점이 된다)
    - answer에 대기 시간과 작업 시간을 더한다. (작업이 종료되는 시점 - 요청 시점)
    - 종료된 작업의 수를 올린다.
4. 만일 작업 큐에 아무 것도 없는 상태(대기 큐에 있는 것들 모두 요청 시점이 되지 않아 기다리는 것)라면 현재 시간을 올린다.

-> 이것을 모든 작업이 끝날 때까지 반복한 후, answer에 jobs.length를 나눠 각 작업의 요청부터 종료까지 걸린 시간의 평균을 리턴한다.

대기 큐와 작업 큐를 두 개 두어 대기 큐는 요청되는 시점으로, 작업 큐는 소요 시간으로 각각 오름차순 정렬한다.

```java
LinkedList<int[]> wait = new LinkedList<>();
PriorityQueue<int[]> run = new PriorityQueue<>(new Comparator<int[]>() {
    @Override
    public int compare(int[] o1, int[] o2) {
        return o1[1] - o2[1];
    }
});

for (int[] arr : jobs) wait.add(arr);
Collections.sort(wait, new Comparator<int[]>() {
    @Override
    public int compare(int[] o1, int[] o2) {
        return o1[0] - o2[0];
    }
});
```

1. 대기 큐에서 요청되는 시점이 현재 시간보다 적은 경우에 작업 큐로 옮긴다.
```java
while (!wait.isEmpty() && wait.peek()[0] <= time) {
    run.add(wait.pollFirst());
}
```

2. 작업 큐에서 소요 시간이 적은 순대로 꺼내 작업한다.
```java
int[] p = run.poll();
```

3. 작업 과정
    - 현재 시간에 작업이 끝나는 시간을 더한다. (작업이 종료되는 시점이 된다)
    - answer에 대기 시간과 작업 시간을 더한다. (작업이 종료되는 시점 - 요청 시점)
    - 종료된 작업의 수를 올린다.
```java
time += p[1];
answer += time - p[0];
count++;
```

4. 만일 작업 큐에 아무 것도 없는 상태(대기 큐에 있는 것들 모두 요청 시점이 되지 않아 기다리는 것)라면 현재 시간을 올린다.
```java
else {
    time++;
}
```

### 전체 코드
```java
import java.util.*;
class Solution {
    public int solution(int[][] jobs) {
        int answer = 0;
        LinkedList<int[]> wait = new LinkedList<>();
        PriorityQueue<int[]> run = new PriorityQueue<>(new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                return o1[1] - o2[1];
            }
        });

        for (int[] arr : jobs) wait.add(arr);
        Collections.sort(wait, new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                return o1[0] - o2[0];
            }
        });
        int count = 0;
        int time = wait.peek()[0];

        while (count < jobs.length) {
            while (!wait.isEmpty() && wait.peek()[0] <= time) {
                run.add(wait.pollFirst());
            }
            if (!run.isEmpty()) {
                int[] p = run.poll();
                time += p[1];
                answer += time - p[0];
                count++;
            } else {
                time++;
            }
        }

        return answer / jobs.length;
    }
}
```