---
title: JPA - 컬렉션과 부가 기능
date: 2020-10-17 13:10:70
update: 2020-10-17 13:10:70
tags:
 - java
description:
---

```java
Team team = new Team();

System.out.println(team.getMembers().getClass());
// java.util.ArrayList

em.persist(team);

System.out.println(team.getMembers().getClass());
// org.hibernate.collection.internal.PerstentBag
```

Hibernate 는 컬렉션을 효율적으로 관리하기 위해 Entity 를 영속 상태로 만들 때 원본 컬렉션을 감싸고 있는 내장 컬렉션을 생성하여 이 내장 컬렉션을 사용하도록 참조를 변경한다. 래퍼 컬렉션이라고도 한다.

하이버네이트는 이런 특징 때문에 `Collection<Member> members = new ArrayList<>();` 처럼 즉시 초기화해서 사용하는 것을 권장한다.

## References

[자바 ORM 표준 JPA 프로그래밍](https://www.aladin.co.kr/shop/wproduct.aspx?itemid=62681446)
