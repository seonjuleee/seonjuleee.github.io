---
layout: post
title: "[프로그래머스] 주식 가격"
categories: [Algorithm]
---

> 문제 링크
> <https://programmers.co.kr/learn/courses/30/lessons/42584>

### 문제 설명

초 단위로 기록된 주식가격이 담긴 배열 prices가 매개변수로 주어질 때, 가격이 떨어지지 않은 기간은 몇 초인지를 return 하도록 solution 함수를 완성하세요.

### 제한 조건

- prices의 각 가격은 1 이상 10,000 이하인 자연수입니다.
- prices의 길이는 2 이상 100,000 이하입니다.

### 입출력 예

|prices|return|
|-|-|
|[1, 2, 3, 2, 3]|[4, 3, 1, 1, 0]|

- 1초 시점의 ₩1은 끝까지 가격이 떨어지지 않았습니다.
- 2초 시점의 ₩2은 끝까지 가격이 떨어지지 않았습니다.
- 3초 시점의 ₩3은 1초뒤에 가격이 떨어집니다. 따라서 1초간 가격이 떨어지지 않은 것으로 봅니다.
- 4초 시점의 ₩2은 1초간 가격이 떨어지지 않았습니다.
- 5초 시점의 ₩3은 0초간 가격이 떨어지지 않았습니다.

### 내 풀이

처음에는 예제만 보고 오른쪽에서부터 min 값을 찾아서 answer 배열에 각각 `인덱스 - (min에 해당하는 인덱스)`를 하면 될 거라고 생각해서 아래와 같은 답을 도출했다.

```java
int minId = prices.length - 1;
for (int i=prices.length - 1; i>=0; i--) {
    if (prices[i] > prices[minId]) {
        answer[i] = minId - i;
    } else {
        minId = i;
        answer[i] = prices.length - 1 - i;
    }
}
```

그러나 문제에 나와 있는 예제만 잘 실행될 뿐, 나머지 테스트 케이스는 실패했다.

예를 들어 `[1, 2, 3, 2, 3, 1]`에는 답이 `[5, 4, 1, 2, 1, 0]`로 나와야 하는데, 내가 짠 코드의 경우 `[5, 4, 3, 2, 1, 0]`을 도출했다. 인덱스가 2일 경우, 2가 아닌 최솟값 1의 인덱스에서 빠지기 때문에 이러한 답이 나온 것이다.

여러가지 생각해보다가 도저히 안되겠어서 다른 코드를 참고했다.

한 블로그에서
> 뒤에서부터 순회를 할 때 앞쪽에 자신보다 작은 숫자가 앞에 있다면 자신은 굳이 스택에 들어가지 않아도 된다는 의미

라는 설명을 보고 작은 숫자를 체크하는 데에 스택을 사용해서 다시 풀어보았다.

Pair 객체를 이용해서 인덱스와 값을 모두 저장하고, for문을 통해서 `prices.length - 2`부터(price.length - 1에서는 값이 항상 0이므로) 자신보다 작은 가장 가까운 수를 체크한다.

오른쪽에 있던 수 중 자신과 가장 가까운 값이 스택에서 먼저 꺼내지기 때문에, 스택에서 차례로 꺼내진 값 중 자신보다 값이 작으면 찾고자 하는 값이 된다.

```java
while (!stack.isEmpty()) {
    if (prices[i] > stack.peek().value) {
        answer[i] = stack.peek().index - i;
        break;
    }
    stack.pop();
}
if (stack.isEmpty()) answer[i] = prices.length - 1 - i;
```

### 전체 코드

```java
import java.util.*;
class Solution {
    public class Pair {
        public int index, value;
        
        public Pair(int index, int value) {
            this.index = index;
            this.value = value;
        }
    }
    
    public int[] solution(int[] prices) {
        int[] answer = new int[prices.length];
        Stack<Pair> stack = new Stack<>();
        stack.add(new Pair(prices.length-1, prices[prices.length-1]));
        for (int i=prices.length - 2; i>=0; i--) {
            while (!stack.isEmpty()) {
                if (prices[i] > stack.peek().value) {
                    answer[i] = stack.peek().index - i;
                    break;
                }
                stack.pop();
            }
            if (stack.isEmpty()) answer[i] = prices.length - 1 - i;
            stack.add(new Pair(i, prices[i]));
        }
        return answer;
    }
}
```

### 참고
<https://codevang.tistory.com/313>