---
title: "[Effective Java] Item 1 - 생성자 대신 정적 팩터리 메서드를 고려하라"
date: 2022-05-02
update: 2022-05-02
tags:
  - Java
  - Effective Java
series: "이펙티브 자바 뽀개기"
---

### 정적 팩터리 메서드
정적 팩터리 메서드(static factory method)는 **클래스의 인스턴스를 반환하는 정적 메서드**이다. 

예를 몇 가지 들어보자.

- `Boolean` 클래스의 `valueOf` 메서드

```java
public static Boolean valueOf(boolean b) {
    return (b ? TRUE : FALSE);
}
```

- `LocalTime` 클래스의 `now` 메서드

```java
public static LocalTime now() {
    return now(Clock.systemDefaultZone());
}
```

두 메서드 다 반환 값이 클래스인 정적 메서드이기 때문에, 객체 생성의 역할을 할 수 있다.

그렇다면 왜 생성자 대신 정적 팩터리 메서드를 고려해야할까?

### 정적 팩터리 메서드가 생성자보다 좋은 이유

#### 1. 이름을 가질 수 있다
생성자의 경우, 매개변수와 생성자 자체만으로는 반환되는 객체의 특성을 제대로 설명하지 못한다.

좀 더 자세히 알아보기 위해 **값이 소수인 `BigInteger`를 반환**하는 예제를 각각 들어보자.

- 생성자의 경우
```java
public BigInteger(int bitLength, Random rnd) {
    ...
}
```

- 정적 팩터리 메서드의 경우
```java
public static BigInteger probablePrime(int bitLength, Random rnd) {
    ...
}
```

정적 팩터리 메서드는 `probablePrime`이라는 메서드 이름을 통해 반환되는 객체의 의미를 좀 더 명확하게 표현할 수 있다.

#### 2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다

정적 팩터리 메서드를 사용하면, 
- 인스턴스를 미리 만들어 놓거나 
- 새로 생성한 인스턴스를 캐싱하여 재활용하는
방식으로 불필요하게 객체를 생성하는 것을 방지할 수 있다.

대표적인 예로, `Boolean.valueOf(boolean)` 메서드는 객체를 아예 생성하지 않는다.

```java
public static Boolean valueOf(boolean b) {
    return (b ? TRUE : FALSE);
}
```

또 다른 예로 로또 번호를 생성하는 메서드를 보자.

```java
public class LottoNumber {
    private static final int MIN_LOTTO_NUMBER = 1;
    private static final int MAX_LOTTO_NUMBER = 45;
    
    private static Map<Integer, LottoNumber> lottoNumberCache = new HashMap<>();
    
    static {
      IntStream.range(MIN_LOTTO_NUMBER, MAX_LOTTO_NUMBER)
                  .forEach(i -> lottoNumberCache.put(i, new LottoNumber(i)));
    }
    
    private int number;
    
    private LottoNumber(int number) {  
      this.number = number;
    }
    
    public LottoNumber of(int number) {  // LottoNumber를 반환하는 정적 팩토리 메서드
      return lottoNumberCache.get(number);
    }
    
    ...
}
```
| 출처 https://velog.io/@ljinsk3/%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C%EB%8A%94-%EC%99%9C-%EC%82%AC%EC%9A%A9%ED%95%A0%EA%B9%8C

정적 팩터리 메서드를 통해 로또 번호 객체를 새로 생성하는 것이 아니라 캐싱을 통해서 불필요한 객체 생성을 막을 수 있다. 

또, 생성자의 접근 제한자를 `private`로 설정하여 객체 생성을 정적 팩터리 메서드로만 가능하게 제한할 수 있다.

#### 3. 반환 타입의 하위 타입 객체를 반환할 수 있다

`Coffee`를 상속받는 하위 클래스 `Americano`, `Latte`, `Espresso`가 있다고 하자.

```java
public class Coffee {
    ...
    public static Coffee of(String ingredient) {
        if (ingredient.equals("water")) {
            return new Americano();
        } else if (ingredient.equals("milk")) {
            return new Latte();
        } else () {
            return new Espresso();
        }
    }
    ...
}
```
정적 팩터리 메서드인 `of`에서는 재료에 따라 원하는 하위 클래스를 반환시킬 수 있다.

이처럼 반환할 객체의 클래스를 자유롭게 선택할 수 있기 때문에, 굉장히 유연하게 만들 수 있다는 장점이 있다.

#### 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다

`EnumSet` 클래스에서는, public 생성자 없이 정적 팩터리만 제공하는데, 원소의 수에 따라 두 가지 하위 클래스(`RegularEnumSet`, `JumboEnumSet`) 중 하나의 인스턴스를 반환한다.

`EnumSet`의 `noneOf` 메서드를 보자. 제네릭 때문에 복잡해 보이지만 밑에 if 문만 보면 된다.
```java
public static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) {
    Enum<?>[] universe = getUniverse(elementType);
    if (universe == null)
        throw new ClassCastException(elementType + " not an enum");

    if (universe.length <= 64)
        return new RegularEnumSet<>(elementType, universe);
    else
        return new JumboEnumSet<>(elementType, universe);
}

```

`noneOf` 메서드는 EnumSet을 반환하는 정적 팩터리 메서드인데, 입력 매개변수인 `elementType`에 의해 결정된 `universe`의 길이가 65를 기점으로 작으면 `RegularEnumSet`을 반환하고, 크면 `JumboEnumSet`를 반환한다.

이처럼 정적 팩터리 메서드를 사용하면 입력 매개변수에 따라 다른 클래스의 객체를 반환할 수 있다.

#### 5. 작성 시점에 반환할 객체의 클래스가 존재하지 않아도 된다

### 정적 팩터리 메서드의 단점

#### 1. 상속 시 정적 팩터리 메서드만 제공하면 하위클래스를 생성할 수 없다
상속을 하게 되면 public이나 protected 생성자가 필요하기 때문이다.
그러나, 상속보다 컴포지션을 사용하도록 유도하고, 불변 타입으로 만들기 위해서는 이 제약을 지켜야하기 때문에 장점이 될 수 있다.

#### 2. 프로그래머가 찾기 힘들다
API 설명이 명확하게 드러나지 않기 때문에 사용자가 사용하는 방식을 알아내야 한다.

### 정적 팩터리 메서드에서 흔하게 사용하는 네이밍 방식
- `from` : 매개변수를 하나 받아서 해당 타입의 객체 생성
- `of` : 여러 매개변수를 받아 적합한 타입의 객체 생성
- `valueOf` : `from`과 `of`의 더 자세한 버전
- `instance | getInstance` : 매개변수로 명시한 인스턴스를 반환. 그러나 같은 인스턴스라는 보장 X
- `create | newInstance` : `instance`, `getInstance`와 같지만, 매번 새로운 인스턴스를 생성해 반환
- `getType | newType` : `getInstance`, `newInstance`와 같으나, 다른 클래스에 팩터리 메서드를 정의할 때 사용
- `type` : `getType`과 `newType`의 간결한 버전

### 참고
- [이펙티브 자바](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=171196410)
- [정적 팩토리 메서드는 왜 사용할까?](https://velog.io/@ljinsk3/%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C%EB%8A%94-%EC%99%9C-%EC%82%AC%EC%9A%A9%ED%95%A0%EA%B9%8C)


