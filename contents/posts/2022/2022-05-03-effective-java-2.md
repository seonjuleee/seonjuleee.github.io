---
title: "[Effective Java] Item 2 - 생성자에 매개변수가 많다면 빌더를 고려하라"
date: 2022-05-03
update: 2022-05-03
tags:
  - Java
  - Effective Java
series: "이펙티브 자바 뽀개기"
---

선택적 매개변수가 많을 때는 정적 팩터리 메서드와 생성자 모두 적절하게 대응하기가 힘든데, **빌더 패턴**을 사용하면 해결 가능하다.

### 점층적 생성자 패턴(Telescoping constructor pattern)

빌더 패턴을 설명하기 전, 비교를 위해 다른 패턴들을 살펴보자. 빌더 패턴 전에는 점층적 생성자 패턴을 사용하여 이 문제를 해결했다고 한다.

`Student` 클래스로 예를 들어 보자.
```java
public class Member {
    private final String name; // 필수
    private final String address; 
    private final String number;
}
```
여기서 매개변수를 선택하여 생성할 수 있도록 만들어보면, 점층적 생성자 패턴의 경우는 다음과 같이 만들어진다.

```java
public class Member {
    private final String name; // 필수
    private final String address; 
    private final String number;

    public Member(String name) {
        this(name, null);
    }

    public Member(String name, String address) {
        this(name, address, null);
    }

    public Member(String name, String address, String number) {
        this.name = name;
        this.address = address;
        this.number = number;
    }
}

```

이 클래스를 생성하려면 원하는 매개변수를 모두 포함한 생성자 중, 가장 짧은 것을 골라 호출하면 된다.

```java
Member member = new Member("홍길동", "서울시", "010-1234-5678");
```

여기서 만약에 `address`를 뺀 `name`, `number` 변수만 가지고 객체를 생성하려면 다음 나타낸 것처럼 `address` 값을 `null`로 주어야 한다.

```java
Member member = new Member("홍길동", null, "010-1234-5678");
```

점층적 생성자 패턴은 
- 사용자가 설정하기를 원치 않는 매개변수도 값을 지정해야함
- 매개변수 개수가 많아지면 클라이언트 코드가 복잡해짐

이런 단점을 가지고 있다.

### 자바빈즈 패턴(JavaBeans pattern)
자바빈즈 패턴은 매개변수가 없는 생성자로 객체를 만든 후, setter 메서드로 매개변수의 값을 설정하는 방식의 패턴이다.

자바빈즈 패턴으로 `Member` 클래스를 나타내보자.

```java
public class Member {
    private String name = null; // 필수
    private String address = null;
    private String number = null;

    public Member() {}

    // setter 메서드
    public void setName(String name) {
        this.name = name;
    }
    public void setAddress(String address) {
        this.address = address;
    }
    public void setNumber(String number) {
        this.number = number;
    }
}
```

자바빈즈 패턴은 점층적 생성자 패턴과는 달리 복잡하지 않지만,
- 객체 하나를 생성하기 위해 메서드를 여러 개 호출해야 함
- 객체 생성 전까지 일관성(consistency)이 무너짐
- 일관성이 무너지기 때문에 불변으로 생성 불가

하다는 단점을 가지고 있다.

### 빌더 패턴(Builder pattern)

빌더 패턴은 빌더 객체를 이용하여 선택 매개변수를 설정할 수 있다.

빌더 패턴으로 만든 `Member` 클래스를 보자.

```java
public class Member {
    private final String name; // 필수
    private final String address;
    private final String number;

    public static class Builder {
        // 필수 매개변수
        private final String name;

        // 선택 매개변수 - 기본값으로 초기화
        private String address = null;
        private String number = null;

        public Builder(String name) {
            this.name = name;
        }

        public Builder address(String val) {
            address = val;
            return this;
        }

        public Builder number(String val) {
            number = val;
            return this;
        }

        public Member build() {
            return new Member(this);
        }
    }

    private Member(Builder builder) {
        name = builder.name;
        address = builder.address;
        number = builder.number;
    }
}
```

`Member` 클래스를 생성하려면 다음처럼 연쇄적으로 메서드를 호출시키면 된다.

```java
Member member = new Member.Builder("홍길동")
                .address("서울시")
                .number("010-1234-5678")
                .build();
```

### 계층적인 클래스에서의 빌더 패턴
빌더 패턴은 계층구조를 가진 클래스에서 사용하기 좋다. 추상 클래스는 추상 빌더를, 구체 클래스(concrete class)는 구체 빌더를 가지게 한다.

책의 예시에 있는 `Pizza` 추상 클래스를 자세하게 보자.

먼저 `Pizza` 클래스는 토핑과 토핑을 `Set`으로 엮은 toppings를 인스턴스로 가진다. 

```java
public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE;}
final Set<Topping> toppings;
```
추상 클래스이므로 클래스를 생성하는 추상 빌더를 이렇게 나타낼 수 있다.

```java
// Pizza.Builder 클래스는 제네릭 타입으로, Builder 타입만 사용되도록 제한되었다고 생각하면 좋다.
abstract static class Builder<T extends Builder<T>> {

    // toppings는 선택 매개변수로 EnumSet.noneOf를 통해 초기화 됨
    EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
    // topping을 더해 자기 자신을 반환
    // Member 예제의 address와 같은 역할
    public T addTopping(Topping topping) {
        toppings.add(Objects.requireNonNull(topping));
        return self();
    }

    abstract Pizza build();

    // 하위 클래스는 이 메서드를 overring하여 "this"를 반환하도록 해야한다.
    // self를 더해 하위클래스에서는 형변환 하지 않고도 메서드 연쇄를 지원할 수 있다.
    protected abstract T self();
    }
}

```

빌더를 통해 `Pizza`를 생성하는 코드는 `Builder`에서 `addTopping`을 통해 추가된 `toppings`를 `Pizza`의 `toppings`에 `clone`하도록 했다.
```java
Pizza(Builder<?> builder) {
    toppings = builder.toppings.clone();
}
```

합치면 `Pizza` 추상 클래스는 이렇게 구성된다.

```java
public abstract class Pizza {

    public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE;}
    final Set<Topping> toppings;

    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
        public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            return self();
        }

        abstract Pizza build();

        protected abstract T self();
    }

    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone();
    }
}
```

추상 클래스로 정의한 `Pizza`에 뉴욕 피자인 `NyPizza`, 칼조네 피자인 `Calzone` 두 하위 클래스가 있다고 하자. 그러면 빌더 패턴으로 나타낸 클래스는 다음처럼 표현된다.

- `NyPizza`
```java
public class NyPizza extends Pizza{
    public enum Size { SMALL, MEDIUM, LARGE }
    private final Size size; // NyPizza에는 size가 필수 매개변수

    public static class Builder extends Pizza.Builder<Builder> {
        private final Size size;

        public Builder(Size size) {
            this.size = Objects.requireNonNull(size);
        }

        // Pizza 클래스에 있는 build 추상 메서드에 NyPizza를 반환하도록 구현
        @Override public NyPizza build() {
            return new NyPizza(this);
        }

        // self도 this를 반환하도록 구현
        @Override
        protected Builder self() {
            return this;
        }
    }

    NyPizza(Builder builder) {
        super(builder);
        size = builder.size;
    }
}
```

- `Calzone`
```java
public class Calzone extends Pizza {
    private final boolean sauceInside; // Calzone에는 sauceInside가 필수 매개변수

    public static class Builder extends Pizza.Builder<Builder> {
        private boolean sauceInside = false; // 기본값

        public Builder sauceInside() {
            sauceInside = true;
            return this;
        }

        // Pizza 클래스에 있는 build 추상 메서드에 Calzone를 반환하도록 구현
        @Override public Calzone build() {
            return new Calzone(this);
        }

        // self도 this를 반환하도록 구현
        @Override protected Builder self() {
            return this;
        }
    }

    private Calzone(Builder builder) {
        super(builder);
        sauceInside = builder.sauceInside;
    }
}
```

이렇게 빌더 패턴을 계층적으로 설계하면 `NyPizza`와 `Calzone`를 구현하는 클라이언트 코드는 간단하게 나타낼 수 있다.

```java
NyPizza pizza = new NyPizza.Builder(NyPizza.Size.SMALL)
        .addTopping(Pizza.Topping.SAUSAGE)
        .addTopping(Pizza.Topping.ONION).build();
Calzone calzone = new Calzone.Builder()
        .addTopping(Pizza.Topping.HAM)
        .sauceInside().build();
```


빌더 패턴은 매우 유연하게 사용할 수 있다는 장점이 크다. Lombok 라이브러리를 사용하면 `@Builder` 어노테이션 하나로 빌더 패턴을 더 쉽게 구현할 수 있다. 


### 참고
- [이펙티브 자바](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=171196410)
