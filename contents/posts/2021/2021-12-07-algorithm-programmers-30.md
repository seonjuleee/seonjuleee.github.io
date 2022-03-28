---
title: "[프로그래머스] 소수 만들기"
date: 2021-12-07
update: 2021-12-07
tags:
  - Algorithm
  - Programmers
---

> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/12977>

### 문제 설명

주어진 숫자 중 3개의 수를 더했을 때 소수가 되는 경우의 개수를 구하려고 합니다. 숫자들이 들어있는 배열 nums가 매개변수로 주어질 때, nums에 있는 숫자들 중 서로 다른 3개를 골라 더했을 때 소수가 되는 경우의 개수를 return 하도록 solution 함수를 완성해주세요.

### 제한사항

- nums에 들어있는 숫자의 개수는 3개 이상 50개 이하입니다.
- nums의 각 원소는 1 이상 1,000 이하의 자연수이며, 중복된 숫자가 들어있지 않습니다.

### 입출력 예

|nums|result|
|-|-|
|[1,2,3,4]|1|
|[1,2,7,6,4]|4|

#### 입출력 예 #1

- [1,2,4]를 이용해서 7을 만들 수 있습니다.

#### 입출력 예 #2

- [1,2,4]를 이용해서 7을 만들 수 있습니다.
- [1,4,6]을 이용해서 11을 만들 수 있습니다.
- [2,4,7]을 이용해서 13을 만들 수 있습니다.
- [4,6,7]을 이용해서 17을 만들 수 있습니다.

### 내 풀이

주어진 숫자 중에서 3개를 더했을 때 소수가 되는 경우의 개수를 구하므로 문제를 아래와 같이 나눌 수 있다.

1. nums 배열에서 숫자 3개를 선택하기
2. 소수를 찾아서 카운팅하기 

1번의 방법은 다양하지만 선택하는 수가 3개로 정해져 있기 때문에 for문을 세 번 사용해서 구현했다. 


```java
class Solution {
    
    public boolean isPrime(int n) {
        for (int i = 2; i*i <= n; i++) {
            for (int j = i; j < n; j+=i) {
                if (n % j == 0) return false;
            }
        }
        return true;
    }
    
    public int solution(int[] nums) {
        int answer = 0;

        for (int i=0; i<nums.length - 2; i++) {
            for (int j=i+1; j<nums.length - 1; j++) {
                for (int k=j+1; k<nums.length; k++) {
                    int sum = nums[i] + nums[j] + nums[k];
                    if (isPrime(sum)) {
                        answer++;
                    }
                }
            }
        }

        return answer;
    }
}
```

### 보완점

소수 찾는 과정에서 에라토스테네스 체 구현 방법과 혼동되어 불필요한 한 줄을 더하게 되었다. 오히려 시간복잡도가 증가하기 때문에 이를 보완하면 다음과 같다.

```java
class Solution {
    
    public boolean isPrime(int n) {
        for (int i = 2; i*i <= n; i++) {
            if (n % i == 0) return false;
        }
        return true;
    }
    
    public int solution(int[] nums) {
        int answer = 0;

        for (int i=0; i<nums.length - 2; i++) {
            for (int j=i+1; j<nums.length - 1; j++) {
                for (int k=j+1; k<nums.length; k++) {
                    int sum = nums[i] + nums[j] + nums[k];
                    if (isPrime(sum)) {
                        answer++;
                    }
                }
            }
        }

        return answer;
    }
}
```

다음에는 숫자 3개를 선택하는 과정에서, for 문을 여러번 사용한 부분을 다른 방식으로도 수정해봐야겠다.