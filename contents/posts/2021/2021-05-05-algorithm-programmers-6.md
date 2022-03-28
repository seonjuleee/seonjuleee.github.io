---
title: "[프로그래머스] 기능개발"
date: 2021-05-05
update: 2021-05-05
tags:
  - Algorithm
  - Programmers
---

> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/42586>

### 문제 설명

프로그래머스 팀에서는 기능 개선 작업을 수행 중입니다. 각 기능은 진도가 100%일 때 서비스에 반영할 수 있습니다.

또, 각 기능의 개발속도는 모두 다르기 때문에 뒤에 있는 기능이 앞에 있는 기능보다 먼저 개발될 수 있고, 이때 뒤에 있는 기능은 앞에 있는 기능이 배포될 때 함께 배포됩니다.

먼저 배포되어야 하는 순서대로 작업의 진도가 적힌 정수 배열 progresses와 각 작업의 개발 속도가 적힌 정수 배열 speeds가 주어질 때 각 배포마다 몇 개의 기능이 배포되는지를 return 하도록 solution 함수를 완성하세요.

### 제한 사항

- 작업의 개수(progresses, speeds배열의 길이)는 100개 이하입니다.
- 작업 진도는 100 미만의 자연수입니다.
- 작업 속도는 100 이하의 자연수입니다.
- 배포는 하루에 한 번만 할 수 있으며, 하루의 끝에 이루어진다고 가정합니다. - 예를 들어 진도율이 95%인 작업의 개발 속도가 하루에 4%라면 배포는 2일 뒤에 이루어집니다.

### 입출력 예

|progresses|speeds|return|
|-|-|-|
|[93, 30, 55]|[1, 30, 5]|[2, 1]|
|[95, 90, 99, 99, 80, 99]|[1, 1, 1, 1, 1, 1]|[1, 3, 2]|

입출력 예 #1
첫 번째 기능은 93% 완료되어 있고 하루에 1%씩 작업이 가능하므로 7일간 작업 후 배포가 가능합니다.
두 번째 기능은 30%가 완료되어 있고 하루에 30%씩 작업이 가능하므로 3일간 작업 후 배포가 가능합니다. 하지만 이전 첫 번째 기능이 아직 완성된 상태가 아니기 때문에 첫 번째 기능이 배포되는 7일째 배포됩니다.
세 번째 기능은 55%가 완료되어 있고 하루에 5%씩 작업이 가능하므로 9일간 작업 후 배포가 가능합니다.

따라서 7일째에 2개의 기능, 9일째에 1개의 기능이 배포됩니다.

입출력 예 #2
모든 기능이 하루에 1%씩 작업이 가능하므로, 작업이 끝나기까지 남은 일수는 각각 5일, 10일, 1일, 1일, 20일, 1일입니다. 어떤 기능이 먼저 완성되었더라도 앞에 있는 모든 기능이 완성되지 않으면 배포가 불가능합니다.

따라서 5일째에 1개의 기능, 10일째에 3개의 기능, 20일째에 2개의 기능이 배포됩니다.

### 내 풀이

1) 작업 일수 구하기

필요한 작업 일수는 `(100 - 작업의 진도) / 작업의 속도`를 통해 구할 수 있다.
그런데 작업 일수가 8.3이 나온다면 올림해 9일이 나와야 하므로 ceil 함수를 통해 올림을 시켜줘야 한다.
여기서 주의해야 할 점은 int 끼리 나눈 값도 int이기 때문에 값을 올림하기 위해 분모나 분자 중 하나를 실수로 형변환을 해줘야 한다. 나도 여기서 틀려서 많이 헤맸고, 다른 사람도 이 부분에서 많이 틀린 듯 했다. 

```java
for (int i=0; i<progresses.length; i++) {
    days[i] = (int) Math.ceil((100 - progresses[i]) / (float)speeds[i]);
}
```

2) 배포되는 기능의 수(return 값) 구하기

최댓값을 저장시키고, 최댓값을 넘어가는 순간 배포가 다음으로 넘어가기 때문에 그 전까지의 count를 세서 answer에 저장해주면 된다.

```java
int count = 1, max = days[0];
    for (int i=1; i<days.length; i++) {
        if (max < days[i]) {
            queue.add(count);
            count = 1;
            max = days[i];
        } else count++;
    }
    queue.add(count);
```

### 전체 코드

```java
import java.util.Queue;
import java.util.LinkedList;

class Solution {
    public int[] solution(int[] progresses, int[] speeds) {
        int[] days = new int[progresses.length];
        Queue<Integer> queue = new LinkedList<>();
        for (int i=0; i<progresses.length; i++) {
            days[i] = (int) Math.ceil((100 - progresses[i]) / (float)speeds[i]);
        }
        int count = 1, max = days[0];
        for (int i=1; i<days.length; i++) {
            if (max < days[i]) {
                queue.add(count);
                count = 1;
                max = days[i];
            } else count++;
        }
        queue.add(count);
        int[] answer = new int[queue.size()];
        int i = 0;
        while(!queue.isEmpty()) {
            answer[i++] = queue.poll();
        }
        return answer;
    }
}

```