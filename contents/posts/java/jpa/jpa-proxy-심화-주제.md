---
title: JPA - Proxy 심화 주제
date: 2020-10-17 15:10:62
update: 2020-10-17 15:10:62
tags:
 - java
description:
---

`Proxy` 로 조회해도 `em.find()` 를 통해 조회해도 엔티티의 동일성은 보장된다. Proxy 를 조회하고 원본 엔티티를 조회하는 것과 순서를 다르게 해서 엔티티를 조회하고 Proxy 를 조회하는 것은 차이가 있다.

먼저 proxy 를 조회하고 원본 엔티티를 조회해보자.

```java
Member refMember = em.getReference(Member.class, "member1");
Member findMember = em.find(Member.class, "member1");


System.out.println(refMember.getClass());
// class jpabook.advanced.Member_$$_jvst843_0
System.out.println(findMember.getClass());
// class jpabook.advanced.Member_$$_jvst843_0

Assert.assertTrue(refMember == findMember);
```

각각의 출력값은 `class jpabook.advanced.Member_$$_jvst843_0` 으로 같다.

Proxy 를 조회 후 같은 식별자 아이디로 조회를 하게 되면 Proxy 가 넘어온다는 것을 알 수 있다. 그 이유는 그래야 그 둘을 비교했을 때 같은 인스턴스임을 보장할 수 있기 때문이다. 그리고 클라이언트로서는 조회한 엔티티가 프록시인지 아닌지 구분하지 않고 사용할 수 있다.

이제 원본 엔티티를 조회하고 프록시를 조회해보자.

```java
Member findMember = em.find(Member.class, "member1");
Member refMember = em.getReference(Member.class, "member1");

System.out.println(refMember.getClass());
// class jpabook.advanced.Member
System.out.println(findMember.getClass());
// class jpabook.advanced.Member

Assert.assertTrue(refMember == findMember);
```

각각의 출력 값은 `class jpabook.advanced.Member` 으로 같다.

원본 엔티티를 먼저 조회하면 영속성 컨텍스트는 원본 엔티티를 이미 데이터베이스에서 조회했으므로 `em.getReference()` 를 호출해도 원본을 반환한다.

## 프록시 타입 비교

프록시는 원본 엔티티를 상속 받아서 만들어지므로 프록시로 조회한 엔티티의 타입을 비교할 때는 `==` 비교를 하면 안 되고 대신에 `instanceof` 를 사용해야 한다.

```java
Member member = new Member("member1");
em.persist(member);
em.flush();
em.clear();

Member refMember = em.getReference(Member.class, "member1");

Assert.assertFalse(Member.class == refMember.getClass());
Assert.assertTrue(refMember instanceof Member);
```

## 프록시 동등성 비교

엔티티의 동등성을 비교하려면 비즈니스 키를 이용해 `equals()` 메소드를 오버라이딩하고 비교하면 된다. 그런데 IDE 나 외부 라이브러리를 사용해 구현한 `equals()` 메소드로 엔티티를 비교할 때, 비교 대상이 원본 엔티티면 문제가 없지만 프록시면 문제가 발생할 수 있다.

지금부터 틀린 예제를 보여주겠다.

```java
@Entity
public class Member {

	@Id
	private String id;
	private String juminNo;

	public String getJuminNo() { return juminNo; }
	public String setJuminNo(String juminNo) { this.juminNo = juminNo; }

	@Override
	public boolean equals(Object obj) {
		if (this == obj) return true;
		if (!(this.getClass() != obj.getClass())) return false; // (1) 설명 참고

		Member member = (Member) obj;

		// (2) 설명참고
		if (name != null ? !name.equals(member.name) :
				member.name != null)
				return false;

		return true;
	}
}
```

위 예제는 name 이 비즈니스 키다. name 이 중복되는 회원이 없다고 가정하자.

(1) 설명

`getClass()` 메소드를 사용해서 비교하면 안 된다. 그 이유는 프록시는 원본 객체를 상속받은 자식 타입이기 때문이다.

따라서 우리는 Proxy 객체가 들어올 것도 고려해 `instanceof` 연산자를 사용해 비교해야 한다.

(2) 설명

`member.name` 으로 접근하게 되면 문제가 생긴다.

Proxy 객체가 들어왔을 때 문제가 생기기 때문이다.

그 이유는, 해당 객체는 멤버 변수에는 아무 값도 없기 때문이다.

따라서 Getter 접근자를 사용해 부모 객체의 멤버 변수를 접근하도록 해야 한다.

다음은 옳은 예제다.

```java
@Entity
public class Member {

	@Id
	private String id;
	private String juminNo;

	public String getJuminNo() { return juminNo; }
	public String setJuminNo(String juminNo) { this.juminNo = juminNo; }

	@Override
	public boolean equals(Object obj) {
		if (this == obj) return true;
		if (!(obj instanceof Member)) return false;

		Member member = (Member) obj;

		if (juminNo != null ? !juminNo.equals(member.getName()) :
				member.getName() != null)
				return false;

		return true;
	}
}
```

**따라서 정리하자면 `Proxy` 의 동등성을 비교할 때는 2가지를 주의해야 한다.**

1. `Proxy` 타입 비교는 `==` 비교 대신에 `instanceof` 를 사용한다.
2. `Proxy` 의 멤버변수에 직접 접근하면 안 되고 대신에 접근자`Getter` 메소드를 사용해야 한다.

## 상속 관계와 프록시

**프록시를 부모 타입으로 조회하면 문제가 발생한다.** 왜냐면 그 `Proxy` 는 그 실제 클래스의 자식과는 관계가 없기 때문에 `instanceof` 연산을 통해 실제 논리적 관계를 알아낼 수 없기 때문이다.

따라서 해결방법은 다음과 같다.

1. JPQL 로 대상 직접 조회
2. 프록시를 벗긴다.

## References

[자바 ORM 표준 JPA 프로그래밍](https://www.aladin.co.kr/shop/wproduct.aspx?itemid=62681446)
