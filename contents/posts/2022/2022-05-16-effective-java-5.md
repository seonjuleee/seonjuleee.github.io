---
title: "[Effective Java] Item 5 - 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라"
date: 2022-05-16
update: 2022-05-16
tags:
  - Java
  - Effective Java
series: "이펙티브 자바 뽀개기"
---

### 의존 관계
한 객체가 다른 객체를 생성하거나 메서드를 호출하면, 그 객체에 의존한다고 표현한다.

책에 예시로 나와있는 `SpellChecker` 클래스는 스펠링이 맞는지 틀린지를 판단하는 클래스이기 때문에, 사전 클래스인 `Dictionary`를 의존한다.

### 의존 관계를 정적 유틸리티 클래스와 싱글턴으로 구현

- 정적 유틸리티 클래스로 구현
```java
public class SpellChecker {
    private static final Dictionary dictionary = new Dictionary();
    
    private SpellChecker() {} // 객체 생성 방지

    public static boolean isValid(String word) {
        ...
    }

    public static List<String> suggestions(String typo) {
        ...
    }
}
```
- 싱글턴으로 구현
```java
public class SpellChecker {
    private final Dictionary dictionary = new Dictionary();
    
    private SpellChecker() {} 
    public static SpellChecker INSTANCE = new SpellChecker();

    public static boolean isValid(String word) {
        ...
    }

    public static List<String> suggestions(String typo) {
        ...
    }
}
```
정적 유틸리티 클래스와 싱글턴으로 구현하게 되면, 유연하지 않고 테스트하기 어렵다는 단점이 있다.

사전은 일반 사전만 있는 것이 아닌 영어 사전, 국어 사전, 불어 사전 등 언어별로 따로 있을 수 있고, 고대 영어 사전과 같이 특수 어휘용 사전을 따로 둘 수도 있다. 

그런데 방금 구현한 방법으로는 여러 가지 사전을 사용하도록 할 수 없다. 사용할 수 있도록 만드려면, `EngSpellChecker`, `KorSpellChecker` 클래스를 만들고 그 안에 각각 해당하는 `EngDictionary`, `KorDictionary` 클래스를 선언하는 복잡한 방식을 거쳐야 할 것 이다.

따라서 사용하는 자원에 따라 동작이 달라지는 클래스의 경우에는, 
```java
    private final Dictionary dictionary = new Dictionary();
```
정적 유틸리티 클래스와 싱글턴처럼 자원을 직접 명시하는 것이 아니라 의존 객체 주입을 사용하면 된다.

### 의존 객체 주입
의존 객체 주입 방법은 인스턴스를 생성할 때 필요한 자원을 넘겨주면 된다.
대신 의존 객체인 이 `Dictionary`는 `interface`여야 한다.

```java
public class SpellChecker {
    private final Dictionary dictionary;

    public SpellChecker(Dictionary dictionary) {
        this.dictionary = dictionary;
    }

    public boolean isValid(String word) {
        ...
    }

    public List<String> suggestions(String typo) {
        ...
    }

}
```

### 참고
- [이펙티브 자바](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=171196410)
- [이펙티브 자바 완벽 공략 1부](https://www.inflearn.com/course/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-1/dashboard)