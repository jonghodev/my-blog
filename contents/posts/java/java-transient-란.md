---
title: Java Transient 란
date: 2021-09-13 21:00:00
update: 2021-09-13 21:00:00
tags:
 - java
description:
---

데이터를 직렬화할 때 비밀번호와 같은 민감한 정보를 제외하고 싶을 때 적용한다.

```java
class Member implements Serializable {
    private String email;
    private String name;
    transient private String password;
}
```
