---
title: "Java - Optional"
date: 2020-10-27
update: 2020-10-27
tags:
  - Java
---

### Optional 클래스
Optional 클래스는 복잡한 조건문 없이 null 값으로 인해 발생하는 예외 처리가 가능하다.

### Optional 객체 생성 메소드

```java
public static <T> Optional<T> empty()
// 비어있는 Optional 객체를 생성

public static <T> Optional<T> of(T value)
// null 값이 들어가면 NullPointerException 발생

public static <T> Optional<T> ofNullable(T value)
// null 값이 들어가면 비어있는 Optional 객체 반환
```

- 예제

```java
import java.util.Optional;

public class OptionalEx {
	public static void main(String[] args) {
		Optional<String> opt1 = Optional.of("opt1");
	    Optional<String> opt2 = Optional.ofNullable("opt2");
	}
}
```
- null을 넣었을 때

```java
import java.util.Optional;

public class OptionalEx {
	public static void main(String[] args) {
		try {
			Optional<String> opt1 = Optional.of(null);
			System.out.println(opt1);
		} catch(NullPointerException npe) {
			System.out.println("opt1은 null");
		}
		
		try {
			Optional<String> opt2 = Optional.ofNullable(null);
			System.out.println(opt2);
		} catch(NullPointerException npe) {
			System.out.println("opt2는 null");
		}
	}
}
```
실행 결과

```
opt1은 null
Optional.empty
```
>opt1은 null 값을 가지기 때문에 NullPointerException이 발생해 `opt1은 null` 결과가 나왔고, <br>opt2는 null 값이 아니라 빈 Optional 객체이기 때문에 `Optional.empty`의 결과가 나왔다.

### 메소드

```java
public Optional<T> filter(Predicate<? super T> predicate)
// 값이 참이면 해당 필터를 통과시키고 거짓이면 통과 X

public T get()
// 값을 가지고 있는 Optional이면 그 값을 반환하고, 빈 객체이면 NoSuchElementException 예외 발생

public boolean isPresent()
// 값이 있으면 true, 없으면 false 반환

public void ifPresent(Consumer<? super T> consumer)
// 값이 있으면 인자에 있는 함수를 수행, 없으면 수행X

public T orElse(T other)
// 저장된 값이 존재하면 그 값 반환, 값이 존재하지 않으면 인수로 전달된 값을 반환

public T orElseGet(Supplier<? extends T> other)
// 저장된 값이 존재하면 그 값 반환, 값이 존재하지 않으면 인수로 전달된 람다 표현식의 결과값을 반환

public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X extends Throwable
// 저장된 값이 존재하면 그 값 반환, 값이 존재하지 않으면 인수로 전달된 예외 발생
```

### 출처
- <http://tcpschool.com/java/java_stream_optional>
- <https://docs.oracle.com/javase/8/docs/api/>
- <https://jdm.kr/blog/234>