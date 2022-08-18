---
title: "[Effective Java] Item 8 - finalizer와 cleaner 사용을 피하라"
date: 2022-05-27
update: 2022-05-27
tags:
  - Java
  - Effective Java
series: "이펙티브 자바 뽀개기"
---

객체를 생성했으면 나중에 사용이 끝났을 때는 없애기도 해야할 것이다. 자바는 두 가지 객체 소멸자를 제공하는데, 바로 `finalizer`와 `cleaner`이다.

그런데, 이 두 소멸자는 기본적으로 사용하지 말아야 한다.

그 이유는 크게 예측할 수 없고, 느리고, 위험하기 때문이다. 좀 더 자세하게 알아보자.

### 자바에서의 객체 회수 방법
C++에서는 자원을 회수하기 위해 destructor가 필요한데, Java에서는 가비지 컬렉터가 이 역할을 담당한다. 따라서 프로그래머는 객체를 회수하려는 작업을 하지 않아도 된다.
또, 비메모리 자원을 회수할 때는 `try-with-resources`와 `try-finally`를 사용하기 때문에 객체 소멸자를 따로 사용할 이유는 더욱이 없는 것이다.

### `finalizer`와 `cleaner`를 사용하지 말아야 하는 이유

1. 즉시 수행된다는 보장이 없다.
- 제때 실행되지 않고 가비지 컬렉터 알고리즘에 의해 실행되는 시간이 결정된다. 
- 실행되기까지 얼마나 걸릴지 알 수 없고 심지어는 실행되지 않을 수도 있다.그렇기 때문에, 중요한 일에 있어서는 큰 오류를 일으킬 수도 있다.

2. 예외를 잡지 못한다.
- `finalizer`는 예외 발생 시 스레드를 중단하여 종료시킨다. 경고조차 출력되지 않으므로 예측하기 어렵다.
- `cleaner`는 스레드를 통제하기 때문에 이러한 문제를 발생시키진 않는다.

3. 심각한 성능 문제를 동반한다.
- `finalizer`가 가비지 컬렉터의 효율을 떨어뜨린다.
- 안전망 방식을 사용하면 훨씬 빨라질 수 있지만, `try-with-resources`를 이용해 가비지 컬렉터가 객체를 수거하도록 하는 방식보다는 느리다.

+) `finalizer` 안전망 방식

4. `finalizer`는 심각한 보안 문제를 일으킬 수도 있다.
- finalizer를 사용하다 예외가 발생하면 가비지 컬렉터가 수집하지 못하게 막는 공격이 발생할 수 있다. 
- 이 공격은 하위클래스의 finalizer가 실행되어 발생하기 때문에 하위 클래스를 생성할 수 없는 `final`로 선언하면 공격을 방지할 수 있다.

### `AutoCloseable`을 사용하여 자원 회수하기

`AutoCloseable`을 구현해 `close` 메서드를 호출시키면 finalizer와 cleaner를 대신할 수 있다.

다만 구현할 때에 인스턴스가 닫혔는지를 추적하는 코드를 추가하면 좋다.

### `cleaner`와 `finalizer`의 적절한 쓰임새
1. 안전망 역할
- 자원을 사용하는 클라이언트에서 close 메서드를 호출하지 않았을 때를 대비해 안전망 역할로 구현할 수 있다.
- FileInputStream, FileOutputStream, ThreadPoolExecutor 등에서는 안전망 역할의 finalizer를 제공하기도 한다.

2. 네이티브 피어(native peer)와 연결된 객체에서 사용
- 네이티브 피어 : 일반 자바 객체가 기능을 위임한 네이티브 객체
- 네이티브 피어는 자바 객체가 아니어서 가비지 컬렉터가 관여할 수 없다. 그러므로 cleaner나 finalizer로 직접 처리할 수 있다.
- 다만, 성능 저하를 감당해야하고, 네이티브 피어가 사용하는 자원을 즉시 회수하지 않아도 되어야 한다. 이 경우가 아니라면 AutoCloseable의 close 메서드를 사용해야 한다.

### `cleaner`를 안전망으로 활용하는 `AutoCloseable` 예시

방(room) 자원을 회수하기 전에 반드시 청소(clean)해야 한다고 할 때, 자동 청소 안전망인 cleaner는 내부안에서 구현되기 때문에, 클래스의 public API에 나타나지 않는다.

```java
public class Room implements AutoCloseable {
    private static final Cleaner cleaner = Cleaner.create();

    // 청소가 필요한 자원. 절대 Room을 참조해서는 안된다
    private static class State implements Runnable {
        int numJunkPiles; // 방(Room) 안의 쓰레기 수

        State(int numJunkPiles) {
            this.numJunkPiles = numJunkPiles;
        }

        // close 메서드나 cleaner가 호출된다.
        @Override
        public void run() {
            System.out.println("방 청소");
            numJunkPiles = 0;
        }
    }
    // 방의 상태. cleanable과 공유한다.
    // 방을 청소할 때 수거할 자원을 담고 있음
    private final State state;

    // cleanable 객체. 수거 대상이 되면 방을 청소한다.
    private final Cleaner.Cleanable cleanable;

    public Room(int numJunkPiles) {
        state = new State(numJunkPiles);
        cleanable = cleaner.register(this, state);
    }

    @Override
    public void close() {
        cleanable.clean();
    }
}
```

`Room`의 클라이언트 코드는 다음과 같다. `Adult` 클래스는 `try-with-resources`를 사용하여 잘 짜인 클라이언트이고, `Teenager` 클래스는 청소를 하지 않는 클라이언트이다. `Teenager`에서는 청소되지 않기 때문에 위에서 미리 만들어둔 안전망 `cleaner`로 청소가 될 것이다. 하지만, 진짜로 청소가 이뤄질지는 보장하지 않는다.(실행 여부 조차 모르기 때문이다.)

```java
public class Adult {
    public static void main(String[] args) {
        try (Room myRoom = new Room(7)) {
            System.out.println("안녕~");
        }
    }
}
```
```java
public class Teenager {
    public static void main(String[] args) {
        new Room(99);
        System.out.println("아무렴");
    }
}
```


### 참고
- [이펙티브 자바](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=171196410)
- [이펙티브 자바 완벽 공략 1부](https://www.inflearn.com/course/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-1/dashboard)
