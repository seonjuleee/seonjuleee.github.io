---
layout: post
title: "유클리드 호제법(최대공약수 구하기)"
categories: [Algorithm]
use_math: true
---

### 최대공약수(gcd, greatest common divisor)
일반적으로 최대공약수를 구하기 위해서는 소인수분해를 하고, 두 수 사이에 공통된 소수를 모두 곱하면 된다. 그런데 이 방식은 수가 커질수록 복잡해지고 시간도 오래 걸린다는 단점이 있다. 

유클리드 호제법을 사용하면 더 효율적으로 최대공약수를 구할 수 있다.

### 유클리드 호제법
>두 정수 $a, b(a > b)$에 대해 $a = bq + r (0 \le r < a)$라 하면, $a, b$의 최대공약수는 $b, r$의 최대공약수와 같다.
<br><br>즉, $gcd(a, b) = gcd(b, r)$


### 알고리즘 구현

```java
public static int gcd(int a, int b) {
    int r;
    while(b > 0) {
        r = a % b;
        a = b;
        b = r;
    }
    return a;
}

```
---
### References

[최대공약수를 구하는 쉬운 방법 — 유클리드 호제법](https://medium.com/@joongi1978/algorithm-2-%EC%B5%9C%EB%8C%80%EA%B3%B5%EC%95%BD%EC%88%98%EB%A5%BC-%EA%B5%AC%ED%95%98%EB%8A%94-%EC%89%AC%EC%9A%B4-%EB%B0%A9%EB%B2%95-%EC%9C%A0%ED%81%B4%EB%A6%AC%EB%93%9C-%ED%98%B8%EC%A0%9C%EB%B2%95-6ea2fa81d114)