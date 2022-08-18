---
title: "[Effective Java] Item 6 - 불필요한 객체 생성을 피하라"
date: 2022-05-17
update: 2022-05-17
tags:
  - Java
  - Effective Java
series: "이펙티브 자바 뽀개기"
---

같은 기능의 객체를 불필요하게 만드는 것보다 재사용하는 것이 좋은데, 각각의 경우를 살펴보자.

### 불필요한 객체를 만드는 예 1 - 문자열

문자열에서 불필요한 객체를 만들게 되는 경우를 알아보자.

```java
// 1. String 인스턴스를 새로 생성
String s1 = new String("apple");

// 2. 하나의 String 인스턴스를 계속해서 사용
String s2 = "apple";
```

첫번째 코드는 String 객체를 계속해서 만드므로, 객체 생성이 불필요하게 될 수 있다.
두번째 코드는 하나의 String 객체를 재사용한다.
따라서 첫번째보다는 두번째 방법을 사용하는 것이 좋다.

이와 같이 불변 클래스에서는 생성자 대신 [정적 팩터리 메서드](https://seonjuleee.github.io/2022-05-02-effective-java-1/)를 사용하면 불필요한 객체 생성을 피할 수 있다.

또 다른 예제를 보자.

정규표현식을 사용하여 복잡한 문자열의 유효성을 판단하는 메서드가 있다.
```java
static boolean isRomanNumeral(String s) {
    return s.matches("^(?=.)M*(C[MD]|D?C{0,3})"
            + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
}
```
여기서 `String.matches`을 사용하면 가장 쉽게 사용할 수 있는 방법이긴 하지만, 한 번 사용되고 버려지기 때문에 생성비용이 큰 정규표현식을 사용하기에는 성능이 좋지 않다.

성능 개선을 위해 `Pattern` 인스턴스를 직접 생성해 캐싱해두고, 메서드 호출 시 이 인스턴스를 재사용하도록 하였다.

```java
public class RomanNumerals {
    private static final Pattern ROMAN = Pattern.compile(
            "^(?=.)M*(C[MD]|D?C{0,3})"
            + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");

    static boolean isRomanNumeral(String s) {
        return ROMAN.matcher(s).matches();
    }
}
```

### 불필요한 객체를 만드는 예 2 - 오토박싱
먼저 오토박싱에 대해 알아보자.

자바에는 기본(primitive)타입과 Wrapper 클래스가 있다.

| 기본 타입 | Wrapper 클래스 |
|-|-|
|byte|Byte|
|short|Short|
|int|Integer|
|long|Long|
|float|Float|
|double|Double|
|char|Character|
|boolean|Boolean|

기본 타입의 데이터를 객체로 취급해야 하는 경우가 있는데 이때 Wrapper 클래스로 변환해 주면 된다. 이때 기본 타입을 Wrapper 클래스로 변환하는 과정을 **박싱(Boxing)**이라고 한다.
반대로, Wrapper 클래스를 다시 기본 타입의 데이터로 꺼내 변환하는 과정을 **언박싱(UnBoxing)**이라고 한다.

이처럼 기본 타입과 Wrapper 클래스를 자바 컴파일러가 자동으로 상호 변환해주는 기술을 **오토박싱/오토언박싱**이라고 한다.

다시 돌아와서, 책에 있는 예시를 한 번 살펴보자.

```java
private static long sum() {
    Long sum = 0L;
    for (long i = 0; i <= Integer.MAX_VALUE; i++)
        sum += i;

    return sum;
}
```

이 메서드는 `sum`을 Wrapper 클래스인 `Long`으로 선언하였는데, for문을 돌 때는 기본 타입의 `long` 타입인 `i`를 `sum`에 더해주고 있다. 

그렇게 되면 `sum`에 더해지기 위해 `long` 타입을 `Long` 타입으로 변환되는 오토박싱이 이루어지고, 이 과정이 `Integer.MAX_VALUE`인 2,147,483,647번 반복되고 있다.

따라서 의도치 않은 오토박싱을 피하기 위해 `sum`을 Wrapper 클래스보단 기본 타입을 사용해야한다.

### 참고
- [이펙티브 자바](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=171196410)
- [이펙티브 자바 완벽 공략 1부](https://www.inflearn.com/course/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-1/dashboard)
- [Wrapper 클래스](http://www.tcpschool.com/java/java_api_wrapper)