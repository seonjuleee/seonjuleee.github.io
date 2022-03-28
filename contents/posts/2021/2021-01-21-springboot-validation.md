---
title: "Spring Boot Validation"
date: 2021-01-21
update: 2021-01-21
tags:
  - Spring Boot
---

### Validation
- 데이터 유효성을 검사하는 로직은
    1. 애플리케이션 전체에 분산
    2. 코드 중복이 심함
    3. 비즈니스 로직에 섞여있음

이라는 문제를 가지고 있어서 기능을 추가, 수정하기 어렵고 오류 발생 가능성도 크다.
- Java에서 Bean Validation이라는 데이터 유효성 검사 프레임워크를 제공하는데, 다양한 제약(Constraint)을 도메인 모델(Domain Model)에 어노테이션(Annotation)으로 간단하게 정의할 수 있어서 위의 문제를 해결할 수 있다.

### gradle 설정
```
implementation('org.springframework.boot:spring-boot-starter-validation')
```	
- `spring-boot-starter-web`을 넣었다면, Bean Validation이 포함되어 있어서 따로 추가할 필요가 없다고 한다.

### 제약 설정
``` java
@Data
public class PersonForm {
    @NotNull
	@Size(min=2, max=30)
	private String name;

	@NotNull
	@Min(18)
	private Integer age;
}
```
- 이런 식으로 어노테이션을 이용해 도메인의 제약 설정을 할 수 있다.

- `@NotNull` : null 검증
- `@Min` : 최솟값 검증
- `@Max` : 최댓값 검증
- `@Size` : 범위 검증
- `@Email` : 이메일 검증
- `@AssertTrue` : true 검증

- `@NotEmpty` : null이나 size가 0 검증
- `@NotBlank` : null이나 whitespace 검증
- `@Positive`, `@PositiveOrZero` : 숫자 검증
- `@Negative`, `@NegativeOrZero` : 숫자 검증
- `@Past`, `@PastOrPresent` : 날짜 검증
- `@Future`, `@FutureOrPresent` : 날짜 검증

### Controller
```java
@PostMapping("/")
public String checkPersonInfo(@Valid PersonForm personForm, BindingResult bindingResult) {

    if (bindingResult.hasErrors()) {
        return "form";
    }

    return "redirect:/results";
}
```
유효성 검사를 하려는 Entity에 `@Valid` 어노테이션을 붙이고, BindingResult를 통해 결과를 받음

### Front End
```html
<td>Name:</td>
<td><input type="text" th:field="*{name}" /></td>
<td th:if="${#fields.hasErrors('name')}" th:errors="*{name}">Name
```
유효성 검사를 하려는 부분에 `th:if`와 `th:errors`를 통해 에러처리를 할 수 있다.

### Validator
- 어노테이션 만으로 유효성 검사를 하는 것은 편리하지만 자유도에 제약이 있는데, Validator로 커스텀을 할 수 있다.

`org.springframework.validation.Validator`에 있는 Validator 인터페이스를 구현하면 된다.

```java
public class PersonValidator implements Validator {

    /**
     * This Validator validates only Person instances
     */
    public boolean supports(Class clazz) {
        return Person.class.equals(clazz);
    }

    public void validate(Object obj, Errors e) {
        ValidationUtils.rejectIfEmpty(e, "name", "name.empty");
        Person p = (Person) obj;
        if (p.getAge() < 0) {
            e.rejectValue("age", "negativevalue");
        } else if (p.getAge() > 110) {
            e.rejectValue("age", "too.darn.old");
        }
    }
}
```

### 출처
- [Validation 어디까지 해봤니?](https://meetup.toast.com/posts/223)
- [Spring Boot - Validation](https://heowc.dev/2018/01/14/spring-boot-hibernate-validation/)
- [Validating Form Input](https://spring.io/guides/gs/validating-form-input/)
- [Validation by Using Spring’s Validator Interface](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#validation)