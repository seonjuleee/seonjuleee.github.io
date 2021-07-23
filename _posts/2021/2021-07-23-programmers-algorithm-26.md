---
layout: post
title: "[프로그래머스] 삼각 달팽이"
categories: [Algorithm]
---

> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/68645>

### 문제 설명

정수 n이 매개변수로 주어집니다. 다음 그림과 같이 밑변의 길이와 높이가 n인 삼각형에서 맨 위 꼭짓점부터 반시계 방향으로 달팽이 채우기를 진행한 후, 첫 행부터 마지막 행까지 모두 순서대로 합친 새로운 배열을 return 하도록 solution 함수를 완성해주세요.

![68645 image]({{"/assets/images/algorithm-programmers-26-1.png"| relative_url}})

### 제한사항

- n은 1 이상 1,000 이하입니다.


### 입출력 예

|n|result|
|-|-|
|4|[1,2,9,3,10,8,4,5,6,7]|
|5|[1,2,12,3,13,11,4,14,15,10,5,6,7,8,9]|
|6|[1,2,15,3,16,14,4,17,21,13,5,18,19,20,12,6,7,8,9,10,11]|

### 내 풀이

세 부분으로 나누어 값을 더하고 빼는 방식으로 동일하게 접근했으나, 이 과정에서 반복적으로 나타내는 방법을 제대로 구현하지 못해 다른 사람들의 풀이를 보게되었다.

삼각 피라미드로 해결하는 것이 아닌, 정사각형으로 보고 삼각형을 왼쪽으로 싹 밀어서 생각하면 훨씬 생각도 쉬워지고 코드도 간단해진다.


```java
class Solution {
    public int[] solution(int n) {
        int[] answer = new int[n * (n+1) / 2];
        int[][] matrix = new int[n][n];
        
        int x = -1, y = 0;
        int num = 1;
        
        // 2차원 배열에 값 삽입
        for (int i=0; i<n; i++) {
            for (int j=i; j<n; j++) {
                if (i % 3 == 0) x++;
                else if (i % 3 == 1) y++;
                else if (i % 3 == 2) { x--; y--; }
                matrix[x][y] = num++;
            }
        }
        
        // 2차원 배열을 1차원 배열로 변경
        int k = 0;
        for (int i=0; i<n; i++) {
            for (int j=0; j<n; j++) {
                if (matrix[i][j] == 0) break;
                answer[k++] = matrix[i][j];
            }
        }

        return answer;
    }
}
```

### 참고
- https://minhamina.tistory.com/58