---
title: "[Effective Java] Item 9 - try-finally보다는 try-with-resources를 사용하라"
date: 2022-06-01
update: 2022-06-01
tags:
  - Java
  - Effective Java
series: "이펙티브 자바 뽀개기"
---

### try-finally

닫아야 하는 자원을 자용할 경우 회수할 때 `try-finally`를 전통적으로 사용하곤 했다. 

```java
static String firstLineOfFile(String path, String defaultVal) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    // try-finally 방식 사용
    try {
        return br.readLine();
    } finally {
        br.close();
    }
}
```

하지만 이 방식이라면, 자원이 둘 이상일 경우에 지저분해진다는 단점이 있다.
또, 예외 사항이 무시되어 디버깅을 어렵게 하기도 한다.

```java
static void copy(String src, String dst) throws IOException {
    InputStream in = new FileInputStream(src);
    // InputStream에 대한 try-finally 문
    try {
        OutputStream out = new FileOutputStream(dst);
        // OutputStream에 대한 try-finally 문
        try {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0)
                out.write(buf, 0, n);
        } finally {
            out.close();
        }
    } finally {
        in.close();
    }
}
```

### try-with-resources
앞서 설명한 문제들은 `try-with-resources`로 쉽게 해결이 가능하다.
이 구조를 사용하기 위해서는 자원이 `AutoCloseable` 인터페이스를 구현하면 된다.

```java
static String firstLineOfFile(String path, String defaultVal) throws IOException {
    // try-with-resouces 방식 사용
    try (BufferedReader br = new BufferedReader(
            new FileReader(path)
    )) {
        return br.readLine();
    } catch (IOException e) {
        return defaultVal;
    }
}
```
```java
static void copy(String src, String dst) throws IOException {
    
    try (
        InputStream in = new FileInputStream(src);
        OutputStream out = new FileOutputStream(dst);
    ) {
        byte[] buf = new byte[BUFFER_SIZE];
        int n;
        while ((n = in.read(buf)) >= 0)
            out.write(buf, 0, n);
    }
}
```

### 참고
- [이펙티브 자바](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=171196410)
