---
title: "[Effective Java] Item 7 - 다 쓴 객체 참조를 해제하라"
date: 2022-05-24
update: 2022-05-24
tags:
  - Java
  - Effective Java
series: "이펙티브 자바 뽀개기"
---

Java의 경우, C, C++과는 달리 다 쓴 객체를 알아서 회수한다. 하지만 메모리 관리에 신경을 써야하는 몇 가지 경우가 있는데, 이러한 경우를 예시와 함께 알아보자. 

### 1. 배열, 리스트 등으로 메모리를 관리하는 경우

책에 있는 예시인 스택 구현 코드를 보자.

```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0) throw new EmptyStackException();
        return elements[--size];
    }

    /*
    * 원소를 위한 공간을 적어도 하나 이상 확보한다.
    * 배열 크기를 늘려야 할 때마다 대략 두 배씩 늘린다.
    * */
    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```

`pop()` 메서드를 보면, `size`가 0일 때 `EmptyStackException` 객체를 생성만 하고, 빼지 않기 때문에 `Object` 배열인 `elements`에 계속해서 쌓이게 될 것이다. 
그렇기 때문에 나중가서는 메모리 누수가 발생할 수 있다.

이를 해결하기 위해서는, 다음과 같이 `pop()` 메서드를 구성하면 된다.

```java
public Object pop() {
    if (size == 0) throw new EmptyStackException();
    Object result = elements[--size];
    elements[size] = null;
    return result;
}
```

뺄 때 null을 이용해 객체 참조 해제를 같이 진행하면 메모리 누수를 막을 수 있게 되는 것이다.

### 2. 캐시로 메모리를 관리하는 경우

캐시에서의 메모리 누수를 살펴보기 위해, 책을 저장하는 캐시를 만들어보자.

먼저, 캐시가 `Map` 형태를 가지므로, key와 value에 해당하는 객체를 생성하겠다.

```java
class Key {
    private Integer id;

    public Key(Integer id) {
        this.id = id;
    }
}
```
`Key` 객체는 간단히 `id` 인스턴스를 가지는 객체로 생성하였다. 

```java
class Book {
    private String author;
    private String category;

    @Override
    public String toString() {
        return "Book{" +
                "author='" + author + '\'' +
                ", category='" + category + '\'' +
                '}';
    }
}
```
`Book` 객체는 책의 저자와 카테고리를 인스턴스로 하였다.

캐시를 관리(setter, getter 등)할 수 있는 `CacheManager`는 다음과 같이 만들었다. 인스턴스는 `Map` 형태의 cache를 가지며, 앞서 만든 Key와 Book 객체를 가지고 값을 넣고 뺄 수 있는 메서드도 함께 생성하였다.

```java
class CacheManager {

    private Map<Key, Book> cache; // cache 인스턴스

    public CacheManager() {
        this.cache = new HashMap<>(); 
    }

    public Book getBookById(Integer id) {
        Key key = new Key(id);
        if (cache.containsKey(key)) {
            return cache.get(key);
        } else {
            Book book = new Book();
            cache.put(key, book);
            return book;
        }
    }

    // cache 값을 가져오는 메서드. 예제에서는 캐시를 가져와 empty인지 확인하는 용도로 사용될 예정
    public Map<Key, Book> getCache() {
        return cache;
    }
}
```

이렇게 캐시 예제를 위해 다 만들었다면, 캐시 안에 값을 넣은 후 `System.gc()`를 이용해 캐시 안을 정리해보자.

+) `System.gc()` : 가비지 컬렉터를 실행하여 메모리를 정리하는 메서드. 실행이 곧바로 된다는 보장은 없다.

```java
public static void main(String[] args) throws InterruptedException {
    CacheManager cacheManager = new CacheManager();
    // cache에 id가 1에 해당하는 키를 가진 Book이 없으면 캐시에 새로 생성해 반환하고, 있으면 해당하는 값을 반환한다.
    cacheManager.getBookById(1);
    System.gc(); // 캐시 정리
    Thread.sleep(3000);

    System.out.println("isEmpty : " + cacheManager.getCache().isEmpty());
}
```

가비지 컬렉터를 실행하여 캐시를 정리한 후 캐시가 지워졌는지를 출력하면 다음과 같이 출력된다.

> 출력 결과
```
isEmpty : false
```

캐시가 제대로 정리되지 않았다는 뜻이다. 이대로 캐시를 사용하면 메모리에 있는 내용은 사라지지 않고 계속 추가되어 결국 메모리 누수가 일어나게 될 것이다.

이를 해결하는 방법은 캐시를 `HashMap`을 사용해서 만드는 것이 아니라 `WeakHashMap`으로 만들면 된다. `WeakHashMap`에 관한 자세한 설명은 [여기](링크)에서 확인할 수 있다.

`WeakHashMap`으로 만든 뒤 코드를 다시 실행해보자.

```java
class CacheManager {
    ...

    public CacheManager() {
        this.cache = new WeakHashMap<>(); 
    }

    ...
}
```
```java
public static void main(String[] args) throws InterruptedException {
    CacheManager cacheManager = new CacheManager();
    // cache에 id가 1에 해당하는 키를 가진 Book이 없으면 캐시에 새로 생성해 반환하고, 있으면 해당하는 값을 반환한다.
    cacheManager.getBookById(1);
    System.gc(); // 캐시 정리
    Thread.sleep(3000);

    System.out.println("isEmpty : " + cacheManager.getCache().isEmpty());
}
```

> 출력 결과
```
isEmpty : true
```

이처럼 캐시를 사용할 경우에는, `WeakHashMap`을 이용하여 메모리 누수를 해결할 수 있다.

#### 백그라운드 스레드(ScheduledThreadPoolExecutor)를 사용하여 캐시 청소하기

`WeakHashMap`을 이용하는 방법 외에도 메모리 관리를 할 수 있다. 캐시 안의 가장 오래된 원소를 주기적으로 청소하는데, 이를 백그라운드 스레드를 이용하여 처리하는 방법이다.

자세한 방법을 알아보기 전, 먼저 `ScheduledThreadPoolExecutor`에 대해 간단하게 알아보자.

`ScheduledThreadPoolExecutor`는 어떤 작업을 일정 시간 지연 후에 수행하거나, 일정 시간 간격으로 주기적으로 실행해야 할 때 사용한다.

`ScheduledThreadPoolExecutor`는 다음과 같은 메서드를 가지고 있다.
- `schedule(Runnable command, long delay, TimeUnit unit)`
    - 일정 시간 뒤에 작업 한 번 실행
- `schedule(Callable command, long delay, TimeUnit unit)`
    - 일정 시간 뒤에 작업 한 번 실행 후, 결과값 리턴
- `scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit)`
    - 일정 시간 간격으로 작업을 반복적으로 실행
- `scheduleWithFixedDelay(Runnable command, long initialDelay, long period, TimeUnit unit)`
    - 작업이 완료되면, 일정 시간 뒤에 다시 실행
    - 작업 종료시점이 기준

예제에서는 `scheduleAtFixedRate()`를 사용하여 반복적으로 오래된 원소를 청소할 것이다.

사용하는 방법은 다음과 같다.

```java
public static void main(String[] args) {

    ScheduledExecutorService executor = Executors.newScheduledThreadPool(1); // 실행할 수 있는 객체 생성. 스레드 풀을 1로 설정하였다. 

    Runnable runnable = () -> System.out.println("Runnable task : " + LocalDateTime.now()); // 스케줄에 따라 스레드에서 실행될 함수 설정
    int delay = 3;

    // 스케줄링
    System.out.println("Scheduled task : " + LocalDateTime.now() );
    executor.schedule(runnable, delay, TimeUnit.SECONDS);
}
```

> 출력 결과

```
Scheduled task : 2022-05-25T21:44:05.329021900
Runnable task : 2022-05-25T21:44:08.406786800
```
설정된 `delay` 시간 뒤에 실행되는 것을 볼 수 있다.

`scheduleAtFixedRate()`를 사용한 예제도 살펴보자.

```java
public static void main(String[] args) {
    ScheduledExecutorService executor = Executors.newScheduledThreadPool(1); // 실행할 수 있는 객체 생성. 스레드 풀을 1로 설정하였다. 

    Runnable runnable = () -> System.out.println("Runnable task : " + LocalDateTime.now()); // 스케줄에 따라 스레드에서 실행될 함수 설정
    int initialDelay = 1; // 최초 실행될 때 발생되는 딜레이 설정
    int period = 3; // 실행 간격 설정

    // 스케줄링
    System.out.println("Scheduled task : " + LocalDateTime.now() );
    executor.scheduleAtFixedRate(runnable, initialDelay, period, TimeUnit.SECONDS);

    Thread.sleep(20000);
    executor.shutdown();
}
```

> 출력 결과
```
Scheduled task : 2022-05-25T21:50:09.463414900
Runnable task : 2022-05-25T21:50:10.525572900
Runnable task : 2022-05-25T21:50:13.522554100
Runnable task : 2022-05-25T21:50:16.514547
Runnable task : 2022-05-25T21:50:19.526542300
Runnable task : 2022-05-25T21:50:22.515494200
Runnable task : 2022-05-25T21:50:25.523446900
Runnable task : 2022-05-25T21:50:28.524457400
```

직접 실행해보면, 스레드가 shutdown되는 20초 동안 지정한 코드가 3초 간격으로 실행되는 것을 볼 수 있다.

그럼 다시 본문으로 돌아와 `ScheduledThreadPoolExecutor`를 이용하여 메모리를 주기적으로 청소하는 예제를 살펴보자.

오래된 원소를 파악하기 위해 `Key` 클래스에 `created` 인스턴스를 추가하였다. 이것으로 원소 생성 시간을 알 수 있다.

```java
class Key {
    private Integer id;
    private LocalDateTime created;

    public Key(Integer id) {
        this.id = id;
        this.created = LocalDateTime.now();
    }

    public LocalDateTime getCreated() {
        return created;
    }
}
```

`Book`, `CacheManager` 클래스는 이전과 동일하다.

```java
public static void main(String[] args) throws InterruptedException {
    ScheduledExecutorService executor = Executors.newScheduledThreadPool(1);
    CacheManager cacheManager = new CacheManager();
    cacheManager.getBookById(1); // cache에 Book 저장

    Runnable runnable = () -> {
            Map<Key, Book> cache = cacheManager.getCache();
            Set<Key> keys = cache.keySet();
            Optional<Key> key = keys.stream().min(Comparator.comparing(Key::getCreated)); // 오래된 key 찾기
            key.ifPresent((k) -> { // 오래된 key에 해당하는 값을 cache에서 지우기
                cache.remove(k);
            });
    };

    executor.scheduleAtFixedRate(runnable, initialDelay, period, TimeUnit.SECONDS);

    Thread.sleep(20000);
    executor.shutdown();
}
```

이렇게하면 지정된 스케줄에 따라 오래된 원소를 삭제하며 주기적으로 메모리를 관리할 수 있다.

### 3. 리스너(listener, 또는 콜백(callback))를 사용하는 경우

리스너를 사용하는 경우도 마찬가지로 어딘가에 리스너를 리스트(맵)에 담아둔 뒤, 이벤트 발생 시 리스너에 해당하는 메서드를 실행하게 된다.

여기서도 리스너 리스트에 있는 원소를 제거하지 않으면 메모리 누수가 발생할 수 있는데, 앞서 설명한 캐시와 동일한 방법으로 메모리 관리를 할 수 있다.

### 참고
- [이펙티브 자바](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=171196410)
- [이펙티브 자바 완벽 공략 1부](https://www.inflearn.com/course/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-1/dashboard)
- [Java - ScheduledThreadPoolExecutor 사용 방법](https://codechacha.com/ko/java-scheduled-thread-pool-executor/)