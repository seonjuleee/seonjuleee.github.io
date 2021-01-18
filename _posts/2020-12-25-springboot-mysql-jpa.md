---
layout: post
title: "Spring Boot JPA, MySQL 연동"
categories: [Spring]
---

### bundle.gradle
```h
// JPA 설정
implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
// mysql 설정
compile 'mysql:mysql-connector-java'
```

### application.properties
```yml
server.address=localhost
server.port=8080

# API 호출시, SQL 문을 콘솔에 출력한다.
spring.jpa.show-sql=true

# DDL 정의시 데이터베이스의 고유 기능을 사용합니다.
# ex) 테이블 생성, 삭제 등
spring.jpa.generate-ddl=true
spring.jpa.database=mysql

# MySQL 설정
spring.datasource.url=jdbc:mysql://localhost:3306/DBNAME?useSSL=false&characterEncoding=UTF-8&serverTimezone=UTC
spring.datasource.username=db아이디
spring.datasource.password=db비번
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# MySQL 상세 지정
spring.jpa.database-platform=org.hibernate.dialect.MySQL5InnoDBDialect
```

### 출처
[Spring Boot - JPA - MySQL 연동](https://velog.io/@2yeseul/Spring-Boot-JPA-MySQL-%EC%97%B0%EB%8F%99)