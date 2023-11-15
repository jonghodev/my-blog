---
title: JPA - 값 타입
date: 2020-10-12 17:10:49
update: 2020-10-12 17:10:49
tags:
 - java
description:
---

값 타입의 실제 인턴스 값을 공유하는 것은 위험하다. 대신에 인스턴스를 복사해서 사용해야 한다.

```java
Address a = new Address("Old");
Address b = a;
b.setCity("New");
```

인스턴스 참조 값을 넘기므로 `Address a` 의 값도 바뀐다.

물론 객체를 대입할 때마다 인스턴스를 복사해서 대입하면 공유 참조를 피할 수 있지만, 문제는 복사하지 않고 원본의 참조 값을 직접 넘기는 것을 막을 방법이 없다는 것이다.

따라서 불변 객체를 만들어야 한다. 가장 간단한 방법은 생성자로만 값을 설정하고 수정자를 만들지 않는 것이다.

```java
int a = 10;
int b = 10;

Address a = new Address("서울시");
Address b = new Address("서울시");
```

`Address a` 와 `Address b` 의 값을 비교할 때는 `Address class` 에서 `equals()` 를 재정의 하여 비교해야 한다.

## equlas() 와 hashCode()

자바에서 `equals()` 를 재정의 하면 `hashCode()` 도 재정의하는 것이 안전하다. 그렇지 않으면 해시를 사용하는 컬렉션(`HashSet`, `HashMap`) 이 정상 동작하지 않는다.

## 동일성와 동등성

동일성 (Identity) 비교: 인스턴스의 참조 값을 비교, `==` 사용

동등성 (Equivalence) 비교: 인스턴스의 값을 비교, `equals()` 사용

## 값 타입을 사용할 때 주의사항

값 타입 컬렉션을 변경했을 때 JPA 구현체들은 테이블의 **기본 키를 식별해서 변경된 내용만 반영**하려고 노력한다. 하지만 사용하는 컬렉션이나 여러 조건에 따라 기본 키를 식별할 수도 있고 식별하지 못할 수도 있다. 따라서 **값 타입 컬렉션을 사용할 대는 모두 삭제하고 다시 저장하는 최악의 시나리오**를 고려하면서 사용해야 한다. 값 타입 컬렉션의 최적화에 관한 내용은 각 구현체의 설명을 참고하자.

> 값 타입 컬렉션은 영속성 전이(Cascade) + 고아 객체 제거(Orphan Remove) 기능을 필수로 가진다고 볼 수 있다.

## References

[자바 ORM 표준 JPA 프로그래밍](https://www.aladin.co.kr/shop/wproduct.aspx?itemid=62681446)
