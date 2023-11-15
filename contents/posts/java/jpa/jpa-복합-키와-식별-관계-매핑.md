---
title: JPA - 복합 키와 식별 관계 매핑
date: 2020-10-12 16:10:49
update: 2020-10-12 16:10:49
tags:
 - java
description:
---

DB 에서는 FK(외래 키)가 기본키에 포함되는지 여부에 따라 식별, 비식별 관계로 구분된다.

보통 비식별 관계를 사용하고 필요한 상황에서 식별 관계를 사용한다.

## 식별 관계

부모 테이블의 기본 키를 내려 받아 <span style="color:orange;font-weight:bold">기본 키 + 외래 키</span>로 사용하는 전략이다.

## 비식별 관계

부모 테이블의 기본 키를 내려 받아 <span style="color:orange;font-weight:bold">외래 키로만 사용하는</span> 전략이다.

필수적 비식별 관계: 외래 키에 null 을 허용 하지 않는다.

선택적 비식별 관계: 외래 키에 null 을 허용한다.

선택적 비식별관계는 항상 외부 조인을 사용해야 하나 필수적 비식별관계는 내부 조인만을 사용할 수 있다.

## 식별 관계 vs 비식별 관계

비식별관계의 장점

1. DB 관점
   1. 식별 관계는 부모 키를 계속 내려받기 때문에 **조인할 때 SQL 이 복잡해지고 기본 키 인덱스가 불필요하게 커질 수 있다.**
   2. 식별 관계를 사용할 때 비즈니스 의미가 있는 자연 키를 사용하는 경우가 많고 비식별 관계는 비즈니스와 전혀 관계 없는 대리키를 주로 사용한다. **비즈니스 요구사항은 시간이 지남에 따라 언젠가는 변한다**. 식별 관계의 자연 키가 자식에 손자까지 전파되면 변경하기 힘들다.
   3. 부모 테이블의 기본 키를 자식 테이블의 기본 키로 사용하므로 **테이블 구조가 유연하지 못하다.**
2. 객체지향 관점
   1. 복합 키의 경우 별도의 클래스로 관리를 해야하므로 비용이 든다.
   2. JPA 는 대리키를 사용하기 위한 `@GenratedValue` 라는 편리한 방법을 제공한다.

식별관계의 장점

1. 기본 키 인덱스를 활용하기 좋고
2. 특정 상황에 조인 없이 하위 테이블만으로 검색을 완료할 수 있다.

이처럼 식별 관계가 가지는 장점도 있으므로 **꼭 필요한 곳에는 적절하게 사용**하는 것이 데이터베이스 테이블 설계의 묘를 살리는 방법이다.

> 가능한 ORM 신규 프로젝트에선, **필수적 비식별관계**를 사용하고 기본 키는 **Long 타입의 대리키**를 사용하자.

## @IdClass vs @EmbeddedId

`@IdClass` 가 데이터베이스에 맞춘 방법이라면 `@EmbeddedId` 는 좀 더 객체지향적인 방법이다.

`@IdClass` 와 다르게 `@EmbbeddId` 를 적용한 식별자 클래스는 식별자 클래스에 직접 매핑한다.

각각 장단점이 있으므로 취향에 맞는 것을 일관성 있게 사용하면 된다.

`@EmbbedId` 가 `@IdClass` 와 비교해서 더 객체지향적이고 중복도 없어 보여서 좋아보이긴 하지만 특정상황에서 JPQL 이 길어 질 수 있다.

```java
em.createQuery("select p.id.id1, p.id.id2 from Parent p"); // EmbeddedId
em.createQuery("select p.id1, p.id2 from Parent p"); // EmbeddedId
```

> 복합 키에는 어떤 컬럼에도 `@GenerateValue` 를 사용할 수 없다.

## 복합 키: 비식별 관계 매핑 - @IdClass 매핑 예제

```java
@Entity
@IdClass(ParentId.class)
public class Parent {

	@Id
	@Column(name = "PARENT_ID1")
	private String id1; // ParentId.id1 과 연결

	@Id
	@Column(name = "PARENT_ID2")
	private String id2; // ParentId.id2 과 연결

	private String name;
}
```

- `@IdClass` Annotation 을 이용해 ParentId 를 매핑했다.

```java
public class ParentId implements Serializable {
	private String id1; // Parent.id1 매핑
	private String id2; // Parent.id2 매핑

	public ParentId() {}

	public ParentId(String id1, String id2) {
		this.id1 = id1;
		this.id2 = id2;
	}

	@Override
	public boolean equals(Object o) {...}

	@Override
	public int hashCode() {...}
}
```

- 식별자 클래스의 속성명과 엔티티에서 사용하는 식별자의 속성명이 같아야 한다.
- `Serializable` Interface 를 구현해야 한다
- `equals`, `hashCode` 를 구현해야 한다.
- 기본 생성자가 있어야 한다.
- 식별자 클래스는 public 이어야 한다.

### 저장과 호출

```java
// 저장
Parent parent = new Parent();
parent.setId1("MyId1"); // 식별자
parent.setId2("MyId2"); // 식별자
parent.setName("parentName");
/* em.persist() 를 호출하면 영속성 컨텍스트에 엔티티를 등록하기 직전에 내부에서
ParentId 를 생성하고 영속성 컨텍스트의 키로 사용한다. */
em.persist(parent);

// 호출
ParentId parentId = new ParentId("MyId1", "MyId2");
Parent parent = em.find(Parent.class, parentId);
```

### 자식 클래스 생성

```java
@Entity
public class Child {

	@Id
	private String id;

	@ManyToOne
	@JoinColumns({
			@JoinColumn(name = "PARENT_ID1",
				referencedColumnName = "PARENT_ID1"),
			@JoinColumn(name = "PARENT_ID2",
				referencedColumnName = "PARENT_ID2"),
	})
	private Parent parent;
}
```

부모 테이블의 기본 키 컬럼이 복합 키이므로 자식 테이블의 외래 키도 복합키다.

따라서 `@JoinColumns` 와 `@JoinColumn` 을 사용하여 외래 키 컬럼을 매핑한다.

## 복합 키: 비식별 관계 매핑 - @EndbeddedId 매핑 예제

```java
@Entity
public class Parent {

	@EmbeddedId
	private ParentId id; // ParentId.id1 과 연결

	private String name;
}
```

```java
@Embeddable
public class ParentId {

	@Column(name = "PARENT_ID1")
	private String id1;

	@Column(name = "PARENT_ID2")
	private String id2;

	public ParentId() {}

	public ParentId(String id1, String id2) {
		this.id1 = id1;
		this.id2 = id2;
	}

	@Override
	public boolean equals(Object o) {...}

	@Override
	public int hashCode() {...}
}
```

- `@EmbbedId` 를 붙여주어야 한다.
- `Serializable` Interface 를 구현해야 한다
- `equals`, `hashCode` 를 구현해야 한다.
- 기본 생성자가 있어야 한다.
- 식별자 클래스는 public 이어야 한다.

### 저장과 호출 코드

```java
// 저장
Parent parent = new Parent();
ParentId parentId = new ParentId("MyKey1", "MyKey2");
parent.setId(parentId);
parent.setName("Parent");
em.persist(parent);

// 호출
ParentId parentId = new ParentId("MyId1", "MyId2");
Parent parent = em.find(Parent.class, parentId);
```

## 복합 키와 equals(), hashCode()

```java
ParentId id1 = new ParentId("myId1", "myId2");
ParentId id2 = new ParentId("myId1", "myId2");

id1.equals(id2) // true 가 나와야한다.
```

복합키를 사용할 경우 `equals` 를 재정의해야 한다. 그렇지 않으면 `Object Class` 의 기본 `equals` 메서드인 참조값 비교 `==` 를 사용하기 때문이다.

## References

[자바 ORM 표준 JPA 프로그래밍](https://www.aladin.co.kr/shop/wproduct.aspx?itemid=62681446)
