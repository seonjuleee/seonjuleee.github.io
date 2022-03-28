---
title: "Thymeleaf 문법 정리"
date: 2020-12-22
update: 2020-12-22
tags:
  - Spring Boot
  - Thymeleaf
---

### 텍스트 표현
- `th:text` : 텍스트 표현하고자 할 때 사용
- `th:utext` : 텍스트 안에 html 태그를 반영해서 표현해줌

```html
<p th:text="'<b>Hello</b>'" />
<p th:utext="'<b>Hello</b>'" />
```

> 결과

- `<b>Hello</b>`
- **Hello**

### 문자와 변수 조합
- `+` : 문자와 변수를 연결하는 연산자
- `|The name is ${name}|` : 치환 처리, 텍스트 내에 변수 값을 넣을 수 있음

```html
<p th:text="'Hello, '+${name}+'!'" />
<p th:text="|Hello, ${name}!|" />
```
→ 둘의 결과는 같다

### 표준 표현식
- `${...}` : 변수
- `*{...}` : 객체의 속성값
- `#{...}` : 메세지
- `@{...}` : 링크 URL
- `~{...}` : Fragment(부분적으로 공통된 화면을 작업)

### 반복, 조건 표현
- `th:each` : 반복문
- `th:switch` : switch-case문
- `th:if` : 조건문
- `th:unless` : 조건문의 else문

### value 설정
- `th:value` : 태그 안의 value 값 지정

### 변수 설정
- `th:with` : 변수값 지정

### 객체 전달
- `th:object` : 객체 전달
- `th:field` : 객체의 속성 전달

### 참고
[Tutorial: Using Thymeleaf](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html)

[Thymeleaf 태그 레퍼런스(2.1.4 버전 기준)](https://indra818.github.io/2017/11/23/thymeleaf-tag-reference/)

[Thymeleaf의 기본 문법 정리(SpringBoot)](https://chung-develop.tistory.com/5)