---
title: "Thymeleaf fragment"
date: 2020-12-23
update: 2020-12-23
tags:
  - Spring Boot
  - Thymeleaf
---

### fragment
- `th:fragment` : 반복되는 부분을 템플릿처럼 지정해두고 여러번 사용할 수 있어서 코드를 더 간결하게 만들어 줄 수 있다.

```html
<footer> th:fragment="copy">
    &copy; 2011 The Good Thymes Virtual Grocery
</footer>
```
반복되는 부분을 `th:fragment`를 이용하여 `copy`라는 이름으로 저장해주고,

```html
<div th:insert="footer :: copy"></div>
<div th:replace="footer :: copy"></div>
<div th:include="footer :: copy"></div>
```
사용할 부분에 `th:insert`, `th:replace`, `th:include`를 적고 뒤에 `footer :: copy`를 입력하면 앞에서의 `copy`가 적용된다.


### 파라미터를 이용해 값 전달
- 공통된 부분은 반복되는데 그 안에서 몇 가지 변경되는 부분이 있다면, 함수처럼 파라미터를 이용하면 된다.

```html
<div th:fragment="frag (onevar,twovar)">
    <p th:text="${onevar} + ' - ' + ${twovar}">...</p>
</div>
```

```html
<div th:replace="::frag (${value1},${value2})">...</div>
<div th:replace="::frag (onevar=${value1},twovar=${value2})">...</div>
```

### insert, replace, include 차이
- `th:insert` : fragment의 태그까지 그대로 가져온다.
- `th:replace` : fragment의 태그로 대체된다.
- `th:include` : content의 태그 그대로 사용한다.

앞에서 사용한 fragment의 예제로 보면,

```html
<footer> th:fragment="copy">
    &copy; 2011 The Good Thymes Virtual Grocery
</footer>
```

```html
<div th:insert="footer :: copy"></div>
<div th:replace="footer :: copy"></div>
<div th:include="footer :: copy"></div>
```
이것들의 결과는 다음과 같다.

```html
<body>
  ...
  <!-- th:insert -->
  <div> 
    <footer>
      &copy; 2011 The Good Thymes Virtual Grocery
    </footer>
  </div>
  <!-- th:replace -->
  <footer>
    &copy; 2011 The Good Thymes Virtual Grocery
  </footer>
  <!-- th:include -->
  <div>
    &copy; 2011 The Good Thymes Virtual Grocery
  </div>
</body>
```

### 클래스 추가
- `th:classappend` : 원하는 조건에 따라 클래스를 추가시킬 수 있다.

```html
<tr th:each="prod : ${prods}" class="row" th:classappend="${prodStat.odd}? 'odd'">
```
---
### 참고
[Tutorial: Using Thymeleaf](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#template-layout)