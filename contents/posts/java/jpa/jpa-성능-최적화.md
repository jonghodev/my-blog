---
title: JPA - 성능 최적화
date: 2020-10-17 16:10:13
update: 2020-10-17 16:10:13
tags:
 - java
description:
---

## N+1 문제

JPA 개발할 때 가장 주의해야하는 것은 **N+1** 문제다. 왜냐면 심각한 성능 문제를 일으키기 때문이다. 예제를 통해 N+1 문제에 대해 알아보자.

```java
@Entity
public class Member {
	@Id @GeneratedValue
	private Long id;

	@OneToMany(mappedBy = "member", fetch = FetchType.EAGER)
	private List<Order> orders = new ArrayList<>();
}
```

```java
@Entity
public class Order {
	@Id @GeneratedValue
	private Long id;

  @ManyToOne
  private Member member;
}
```

### 즉시 로딩과 N+1

```java
em.find(Member.class, id)
```

조회한 SQL 문은 다음과 같다.

```sql
SELECT M.*, O.*
FROM
		MEMBER M
OUTER JOIN ORDERS O ON M.ID=O.MEMBER_ID
```

조인을 이용해 한 번에 조회하는 것을 볼 수 있다. 여기까지는 로직이 괜찮지만 문제는 JPQL 을 사용할 때 발생한다.

```java
List<Member> members =
		em.createQuery("select m from Member m", Member.class)
		.getResultList();
```

JPQL 을 실행하면 JPA 는 이것을 분석해서 SQL 을 생성한다. 이때 즉시 로딩과 지연 로딩에 대해서 전혀 신경쓰지 않고 JPQL 만 이용해서 SQL 을 생성한다. 따라서 다음과 같은 SQL 이 실행된다.

```sql
SELECT * FROM MEMBER
```

SQL 의 실행 결과로 먼저 회원 엔티티를 로딩한다. 그런데 글로벌 전략이 즉시 로딩으로 설정되어 있으므로 JPA 는 주문 컬렉션을 즉시 로딩하려고 다음 SQL 을 List 의 수만큼 추가로 실행한다.

```sql
SELECT * FROM ORDERS WHERE MEMBER_ID=?
SELECT * FROM ORDERS WHERE MEMBER_ID=?
SELECT * FROM ORDERS WHERE MEMBER_ID=?
SELECT * FROM ORDERS WHERE MEMBER_ID=?
SELECT * FROM ORDERS WHERE MEMBER_ID=?
...
```

### 지연 로딩과 N + 1

```java
@Entity
public class Member {
	@Id @GeneratedValue
	private Long id;

	@OneToMany(mappedBy = "member", fetch = FetchType.LAZY)
	private List<Order> orders = new ArrayList<>();
}
```

다음과 같이 지연 로딩을 설정하면 JPQL 에서는 N+1 문제가 발생하지 않는다.

```java
List<Member> members =
		em.createQuery("select m from Member m", Member.class)
		.getResultList();
```

하지만 이후 비즈니스 로직에서 주문 컬렉션을 실제 사용할 때 지연 로딩이 발생한다.

```java
for (Member member : members) {
  System.out.println("member = " + member.getOrders().size());
}
```

```sql
SELECT * FROM ORDERS WHERE MEMBER_ID=1
SELECT * FROM ORDERS WHERE MEMBER_ID=2
SELECT * FROM ORDERS WHERE MEMBER_ID=3
SELECT * FROM ORDERS WHERE MEMBER_ID=4
SELECT * FROM ORDERS WHERE MEMBER_ID=5
...
```

결국 위 문제는 글로벌 전략을 지연 로딩으로 설정하여도 비즈니스 로직에서는 어차피 실제 사용할 때 로딩이 발생하므로 똑같은 N+1 문제가 발생하게 된다.

N+1 문제를 해결할 수 있는 다양한 방법을 알아보자.

### 해결방법

1. 페치 조인 사용

다음과 같이 한 번에 조회할 수 있다.

```sql
select m from Member m join fetch m.orders
```

2. Hibernate 의 `@BatchSize` 사용

3. Hibernate 의 `@Fetch(FetchMode.SUBSELECT` 사용

### 정리

가능하면 모든 연관관계 매핑에서 **지연 로딩을 사용하는 것이 좋다.** 즉시 로딩 전략은 그럴듯해 보이지만 N+1 문제는 물론이고 비즈니스 로직에 필요하지 않은 엔티티를 로딩해야하는 상황이 자주 발생한다. 그리고 성능 최적화가 힘들다. 따라서 가능하면 모든 곳에서 지연 로딩을 사용하고 성능 최적화가 꼭 필요한 곳에서 JPQL 페치 조인을 사용하자.

> 기본값이 즉시 로딩인 `@OneToOne` 과 `@ManyToOne` 은 지연 로딩 전략으로 바꿔주자.

## 읽기 전용 쿼리의 성능 최적화

엔티티가 영속성 컨텍스트에 관리되면 1차 캐시부터 변경 감지까지 얻을 수 있는 해택이 많다. 하지만 영속성 컨텍스트는 변경 감지를 위해 스냅샷 인스턴스를 보관하므로 더 많은 메모리를 사용하는 단점이 있다. 만약 조회만 하는 엔티티가 있다면 성능 최적화를 위해 읽기 전용으로 엔티티를 조회를 할 수 있다.

방법은 다음과 같다.

1. 스칼라 타입으로 조회

```sql
select o.id, o.name, o.price from order o
```

2. 읽기 전용 쿼리 힌트 사용

```java
TypedQuery<Order> query = em.createQuery("select o from Order o"), Order.class);
query.setHint("org.hibernate.readOnly", true);
```

읽기 전용으로 조회했으므로 영속성 컨텍스트는 스냅샷을 보관하지 않는다. 따라서 메모리를 최적화 할 수 있다. 단 스냅샷이 없으므로 엔티티를 수정해도 데이터베이스에 반영되지 않는다.

3. 읽기 전용 트랜잭션 사용

```java
@Transactional(readOnly = true)
```

트랜잭션을 시작했으므로 트랜잭션 시작, 로직수행, 트랜잭션 커밋의 과정은 이루어진다. 그러나 영속성 컨텍스트를 플러시 하지 않아, 플러시할 때 이루어지는 스냅샷 비교와 같은 무거운 로직들을 수행하지 않아 성능이 향상된다.

4. 트랜잭션 밖에서 읽기

```java
@Transactional(propagation = Propagation.NOT_SUPPORTED)
```

기본적으로 플러시 모드는 AUTO 로 설정되어 있다. 따라서 트랜잭션을 커밋하거나 쿼리를 실행하면 플러시가 작동한다. 그런데 트랜잭션 자체가 존재하지 않으므로 트랜잭션을 커밋할 일이 없다.

스프링 프레임워크에서는 읽기 전용 트랜잭션과 읽기 전용 쿼리 힌트를 동시에 사용하는 것이 효과적이다.

```java
@Transactional(readOnly = true)
public List<DataEntity> findDatas() {
	return em.createQuery("select d from DataEntity d",
			DataEntity.class);
			.setHint("org.hibernate.readOnly", true);
			.getResultList();
}
```

1. 읽기 전용 트랜잭션 사용: 플러시를 작동하지 않도록 해서 성능 향상
2. 읽기 전용 엔티티를 사용: 엔티티를 읽기 전용으로 조회해서 메모리 절약

## 배치 처리

수백만 건의 데이터를 배치 처리해야 하는 상황이라 가정해보자. 일반적인 방식으로 엔티티를 계속 조회하면 영속성 컨텍스트에 아주 많은 엔티티가 쌓이므로 메모리 부족 오류가 발생한다. 따라서 이런 배치 처리는 적절한 단위로 영속성 컨텍스트를 초기화 해야 한다. 또한, 2차 캐시를 사용하고 있다면 2차 캐시에 엔티티를 보관하지 않도록 주의해야 한다.

등록 배치

```java
for (int i=0; i<10000; i++) {
		Product product = new Product("item" + i, 10000);
		em.persist(product);

		// 100 건마다 플러시와 영속성 컨텍스트 초기화
		if (i % 100 ==0) {
					em.flush();
				em.clear();
		}
}
```

배치 처리는 아주 많은 데이터를 조회해서 수정한다. 이때 수많은 메모리를 한 번에 메모리에 올려둘 수 없어서 2가지 방법을 주로 사용한다.

1. 페이징 처리: 데이터베이스 페이징 기능을 사용한다.
2. 커서: 데이터베이스가 지원하는 커서 기능을 사용한다.

수정 배치: 페이징 Example

```java
int pageSize = 100;
for (int i=0; i<10000; i++) {
		List<Product> resultList = em.createQuery("select p from Product p",
				Product.class)
						.setFirstResult(i * pageSize);
						.setMaxResulsts(pageSize)
						.getResultList();

		// 비즈니스 로직 수행
		for (Product product : resultList) {
				product.setPrice(product.getPrice() + 100);
		}
		em.flush();
		em.clear();
}
```

## 트랜잭션을 지원하는 쓰기 지연과 성능 최적화

```java
insert(member1); // INSERT INTO ...
insert(member2); // INSERT INTO ...
insert(member3); // INSERT INTO ...
insert(member4); // INSERT INTO ...
insert(member5); // INSERT INTO ...
commit();
```

네트워크 호출은 단순한 메소드를 수만 번 호출하는 것보다 더 큰 비용이 든다. 위 코드는 5번의 INSERT SQL 과 1번의 커밋으로 총 6번 데이터베이스와 통신한다. 이것을 최적화 하려면 5번의 INSERT SQL 을 모아서 한 번에 데이터베이스로 보내면 된다. JDBC 가 제공하는 SQL 배치 기능을 사용해도 되지만 코드의 많은 부분을 수정해야 하므로, JPA 의 플러시 기능을 이용해 쉽게 구현할 수 있다.

`hibernate.jdbc.batch_size` 속성의 값에 50을 주면 최대 50건씩 모아서 SQL 배치를 실행한다. 하지만 SQL 배치는 같은 SQL 일 때만 유효한다. 중간에 다른 처리가 들어가면 배치를 다시 시작한다.

예를들어보면 아래의 코드는 총 3번 SQL 배치를 실행한다.

```java
em.persist(new Member()); // 1
em.persist(new Member()); // 2
em.persist(new Member()); // 3
em.persist(new Member()); // 4
em.persist(new Child()); // 5, 다른 연산
em.persist(new Member()); // 6
em.persist(new Member()); // 7
```

### 주의사항

엔티티가 영속 상태가 되려면 식별자가 꼭 필요하다. 그런데 IDENTITY 식별자 생성 전략은 엔티티를 데이터베이스에 저장해야 식별자를 구할 수 있으므로 `em.persist()` 를 호출하는 즉시 `INSERT SQL` 이 데이터베이스에 전달된다. 따라서 쓰기 전략을 활용한 성능 최적화를 할 수 없다.

## 트랜잭션을 지원하는 쓰기 지연과 애플리케이션 확장성

트랜잭션을 지원하는 쓰기 지연과 변경 감지 기능 덕분에 성능과 개발의 편의성이라는 두 마리 토끼를 모두 잡을 수 있었다. 하지만 진짜 장점은 **데이터베이스 테이블 로우에 락을 걸리는 시간을 최소화**한다는 점이다.

다음 로직을 보자.

```java
update(memberA); // UPDATE SQL A
비즈니스 로직 1 // UPDATE SQL ...
비즈니스 로직 2 // INSERT SQL ...
```

JPA 를 사용하지 않고 SQL 을 직접 다루면 `update(memberA)` 를 호출할 때 UPDATE SQL 을 실행하면서 데이터베이스 테이블 로우에 락을 건다. 이 락은 비즈니스 로직 1, 2 를 모두 수행하고 `commit()` 을 호출할 때까지 유지된다. 트랜잭션 격리수준(Isolation Level)에 따라 다르지만 보통 많이 사용하는 `Read Commited` 격리 수준이나 그 이상에서는 데이터베이스 현재 수정 중인 데이터를 수정하려는 다른 트랜잭션은 락이 풀릴 때가지 대기한다.

JPA 는 커밋을 해야 플러시를 호출하고 데이터베이스에 수정 쿼리를 보낸다. 예제에서 `commit()` 을 호출할 때 모든 SQL 문을 바로 커밋하므로 데이터 베이스에 락이 걸리는 시간을 최소화한다.

사용자가 증가하면 애플리케이션 서버를 증설하면 된다. 하지만 데이터베이스 락은 애플리케이션 서버 증설만으로는 해결할 수 없다. 오히려 애플리케이션 서버를 증설해서 트랜잭션이 증가할수록 더 많은 데이터베이스 락이 걸린다. JPA 쓰기 지연 기능은 데이터베이스 락이 걸리는 시간을 최소화해서 동시에 더 많은 트랜잭션을 처리할 수 있는 장점이 있다.

## 정리

- 같은 영속성 컨텍스트의 엔티티를 비교할 때는 동일성 비교를 할 수 있지만 영속성 컨텍스트가 다르면 동일성 비교에 실패한다. 따라서 자주 변하지 않는 비즈니스 키를 사용한 동등성 비교를 해야 한다.
- 프록시를 사용하는 클라이언트는 조회한 엔티티가 프록시인지 아닌지 구분하지 않고 사용할 수 있어야 한다. 하지만 프록시는 기술적인 한계가 있으므로 한계점을 인식하고 사용해야 한다.
- JPA 를 사용할 때는 N+1 문제를 가장 조심해야 한다. N+1 문제는 주로 페치 조인을 사용해서 해결한다.
- 엔티티를 읽기 전용으로 조회하면 스냅샷을 유지할 필요가 없고 영속성 컨텍스트를 플러시하지 않아도 된다.
- 대량의 엔티티를 배치 처리하려면 적절한 시점에 꼭 플러시를 호출하고 영속성 컨텍스트로 초기화해야 한다.
- JPA 은 SQL 쿼리 힌트를 제공하지 않지만 하이버네이트 구현체는 제공한다.
- 트랜잭션을 지원하는 쓰기 지연 덕분에 SQL 배치 기능을 사용할 수 있다.

## References

[자바 ORM 표준 JPA 프로그래밍](https://www.aladin.co.kr/shop/wproduct.aspx?itemid=62681446)
