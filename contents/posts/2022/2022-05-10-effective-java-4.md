---
title: "[Effective Java] Item 4 - 인스턴스화를 막으려거든 private 생성자를 사용하라"
date: 2022-05-10
update: 2022-05-10
tags:
  - Java
  - Effective Java
series: "이펙티브 자바 뽀개기"
---

### 정적 멤버만 담은 유틸리티 클래스
정적 메서드와 정적 필드만을 담은 단순한 클래스는 다음과 같이 사용된다.

- 기본 타입 값이나 배열 관련 메서드 모으기
    - ex. `java.lang.Math`, `java.util.Arrays`
- 인터페이스를 구현하는 객체를 생성해주는 정적 메서드(또는 팩터리)를 모으기
    - ex. `java.util.Collections`
- final 클래스와 관련한 메서드 모으기
    - final 클래스를 상속받아 메서드에 넣을 수 없기 때문이다.

이런 클래스는 인스턴스로 만들어 쓰려고 만들어진 것이 아니다.
그러나, 생성자를 명시하지 않으면 컴파일러가 자동으로 기본 생성자를 만들어준다.
따라서 인스턴스화를 막아야한다.

인스턴스화를 막는 방법은 아주 간단하다. 
private 생성자를 추가하면 된다.

```java
public class UtilityClass {
    // 기본 생성자가 만들어지는 것을 막는다(인스턴스 방지용).
    private UtilityClass() {
        throw new AssertionError();
    }
}
```

꼭 `AssertionError`를 던질 필요는 없지만 실수로라도 생성자를 호출하지 않도록 해준다.
다만 생성자가 존재하는데 호출할 수 없으므로, 예시처럼 적절한 주석을 달아주면 좋다.

### 참고
- [이펙티브 자바](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=171196410)
