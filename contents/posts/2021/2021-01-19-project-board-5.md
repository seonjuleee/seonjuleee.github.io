---
title: "게시판 프로젝트 개발 일지 - 5"
date: 2021-01-19
update: 2021-01-19
tags:
  - Project
  - Spring Boot
series: "게시판 프로젝트"
---

2021.01.19 개발 일지

### 오늘 진행한 내용
- 유튜브 <b>[코딩의신 - Spring Boot으로 웹 출시까지](https://www.youtube.com/watch?v=FYkn9KOfkx0&list=PLPtc9qD1979DG675XufGs0-gBeb2mrona)</b> 5강과 6강 이어서 학습

1. JPA로 게시판 조회
- @Entity : 객체와 테이블 매핑
- @Id : pk 설정
- @GeneratedValue : auto increment 설정
    + AUTO : 기본 값. 아래 세 가지 중 한 가지로 자동 설정됨
    + IDENTITY : DB 컬럼 사용. 보통 많이 사용
    + SEQUENCE : DB 시퀀스 사용. 가장 성능이 좋으나 따로 만들어야해서 관리하기 복잡함
    + TABLE : 테이블을 따로 만들어 저장해서 사용.

2. 게시판 글쓰기 기능
- BoardController에서 GET으로 모델을 새로 생성해서 form.html 파일에 넘겨주고, form.html에서 POST으로 데이터를 받아 BoardController에서 저장하도록 함
- Thymeleaf 문법
    + `th:object` : 객체 전달
    + `th:field` : 속성 전달
    + `*{...}` : 객체의 속성값 

3. 게시글 수정 기능
- 글쓰기와 수정 기능의 메서드를 각각 분리해서 만드는 것이 아니라 하나의 메서드에서 id값의 유무에 따라 글쓰기와 수정을 결정.
- BoardController에서 @RequestParam을 통해 id를 받고, id가 null이면 글쓰기로 가고 id가 있으면 해당하는 id를 가지는 객체를 수정하도록 함.