---
title: "[Effective Java] Item 3 - private 생성자나 열거 타입으로 싱글턴임을 보증하라"
date: 2022-05-09
update: 2022-05-09
tags:
  - Java
  - Effective Java
series: "이펙티브 자바 뽀개기"
---

### 싱글턴(singleton)
객체의 인스턴스를 오직 1개만 생성할 수 있는 클래스. 생성자의 호출이 반복적으로 이뤄져도 객체는 최초 생성된 객체를 반환해준다.

### 클래스를 싱글턴으로 만드는 이유?
- 메모리 낭비를 방지할 수 있다
    - 최초 한 번 생성하면 이후에도 고정된 메모리 영역을 사용하기 때문에 객체에 접근할 때 메모리 낭비를 방지할 수 있다.
- 다른 클래스 간에 데이터 공유가 쉽다
    - 싱글턴 인스턴스가 전역으로 사용되기 때문에 다른 클래스의 인스턴스들이 접근하여 사용 가능하다.

### 싱글턴의 단점
- 싱글톤을 구현하는 코드 자체가 많이 필요하다
- 테스트하기 어렵다
    - 테스트를 할 때 마다 매번 인스턴스의 상태를 초기화시켜주어야 한다.
- 멀티스레딩 환경에서 발생할 수 있는 동시성 문제 해결을 해결해야 한다
- 해당 싱글턴 객체를 사용하는 다른 객체 간의 결함도가 높아져 객체 지향 설계 원칙에 어긋나게 된다
- 자식클래스를 만들 수 없다

### 싱글턴을 만드는 방식 1 - public static final 필드 방식
생성자는 `private`로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 `public static` 멤버를 `final`으로 하나 마련한다.

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() {} // 생성자

    public void leaveTheBuilding() {}
}
```
생성자는 `Elvis.INSTANCE`를 초기화할 때 딱 한 번 호출된다. 생성자가 `private`이므로, `publi`c이나 `protected` 생성자가 없기 때문에 `Elvis` 클래스가 초기화될 때 만들어진 인스턴스가 전체 시스템에서 클래스가 하나인 것으로 보증된다.

이렇게 만들면 간결하면서도 해당 클래스가 싱글턴임을 API에 명백하게 드러낼 수 있다.

### 싱글턴을 만드는 방식 2 - 정적 팩터리 방식의 싱글턴

정적 팩터리 메서드를 `public static`으로 만드는 방식이다.

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() {} // 생성자
    public static Elvis getInstance() {
        return INSTANCE;
    }

    public void leaveTheBuilding() {}
}
```

정적 팩터리 메서드인 `Elvis.getInstance`는 항상 같은 객체의 참조를 반환하도록 되어있다. 

이 방식의 장점
- API를 바꾸지 않고도 싱글턴이 아닌 다른 것으로 변경할 수 있다
- 정적팩터리를 제네릭 싱글턴 팩터리로도 만들 수 있다
- 정적 팩터리의 메서드 참조를 공급자(supplier)로 사용할 수 있다

### 싱글턴을 만드는 방식 3 - 원소가 하나인 열거 타입 선언
public 필드 방식과 비슷하지만, 클래스를 열거 타입으로 만드는 방법이다. 싱글턴 생성 방법 중 가장 바람직한 방법이다.

```java
public enum Elvis {
    INSTANCE;

    public void leaveTheBuilding() {}
}
```

이 방식의 장점
- 더 간결하다
- 직렬화를 간단하게 할 수 있다


### 참고
- [이펙티브 자바](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=171196410)
- [싱글톤(Singleton) 패턴이란?](https://tecoble.techcourse.co.kr/post/2020-11-07-singleton/)
