---
title: JPA - 웹 애플리케이션과 영속성 관리
date: 2020-10-14 13:10:15
update: 2020-10-14 13:10:15
tags:
 - java
description:
---

스프링 컨테이너의 기본 전략

스프링 컨테이너는 트랜잭션 범위의 영속성 컨텍스트 전략을 기본으로 사용한다.

→ **트랜잭션의 범위와 영속성 컨텍스트의 생존 범위가 같다.**

스프링이나 J2EE 컨테이너 환경에서 JPA 를 사용하면 트랜잭션의 범위와 영속성 컨텍스트의 생존 범위가 같다. 그리고 같은 트랜잭션 안에서는 항상 같은 영속성 컨텍스트에 접근한다.

이 전략은 트랜잭션이라는 단위로 영속성 컨텍스트를 관리하므로 트랜잭션을 커밋하거나 롤백할 때 문제가 없다. 이 전략의 유일한 단점은 프리젠테이션 계층에서 엔티티가 준영속 상태가 되므로 지연 로딩을 할 수 없다는 점이다.

지연 로딩 문제를 해결하기 위한 방법이다.

- Global Fetch 전략을 Eager 로 설정
- JPQL Fetch Join 사용
- FACADE 계층 추가
- OSIV 사용

OSIV 는 과거에 사용하던 방식과 최근에 나온 방식이 있다. 아래에서 설명하겠다.

## OSIV 의 과거 방식

1. Transaction per request
   클라이언트의 요청이 들어오자마자 서블릿 필터나 스프링 인터셉터에서 트랜잭션을 시작하고 요청이 끝날 때 트랜잭션도 끝낸다.

단점: 프리젠테이션 레이어에서 데이터를 수정 시 영속성 컨텍스트의 변경 감지 기능이 작동해서 변경된 엔티티를 데이터베이스에 반영한다.

```java
class MemberController {
	public String (Long id) {
		Member member = memberService.getMember(id);
		member.setName("XXX"); // 보안상의 이유로 고객 이름 변경
		model.addAttribute("member", member");
	}
}
```

2. 엔티티를 읽기 전용 인터페이스로 제공

3. 엔티티 레핑

4. DTO만 반환

위 방식들 모두 코드 수가 늘어난다.

## OSIV 의 기존 방식

**Spring Framework 가 제공하는 OSIV 라이브러리를 사용하는 것이다.**

동작 원리는 클라이언트의 요청이 들어오면 영속성 컨텍스트를 생성하고. 서비스 계층에서 트랜잭션을 시작하면 앞에서 만든 영속성 컨텍스트를 사용하고 서비스 계층이 끝나면 커밋을 하면서 영속성 컨텍스트를 플러시 하는 것이다. 이때 트랜잭션만 종료하고 영속성 컨텍스트는 살려두어 프리젠테이션 레이어에서도 지연 로딩이 가능하게 된다.

Presentation Layer 에서 트랜잭션이 끝났음에도 불구하고 영속성 컨텍스트가 살아있다면 조회는 할 수 있다. 이것을 `Nontransactional reads` 라고 한다.

만약 Presentation Layer 에서 데이터를 수정한 상태에서 요청이 끝나게 되면 스프링이 제공하는 OSIV Servlet Filter 나 OSIV Spring Interceptor 가 요청이 끝났을 때 `em.close()` 로 영속성 컨텍스트만 종료해 버리므로 `Flush` 가 일어나지 않는다. 강제로 Flush 하려 해도 `TransactionRequiredException` 이 발생한다.

단점은, 엔티티를 수정하고 즉시 뷰를 호출한 것이 아니라 다시 비즈니스 로직을 호출하면 그 트랜잭션이 변경된 영속성 컨텍스트를 플러시해서, 변경 감지 기능이 작동해 수정사항을 데이터베이스에 반영하게 된다. 예제코드는 아래와 같다.

```java
class MemberController {
	public String (Long id) {
		Member member = memberService.getMember(id);
		member.setName("XXX"); // 보안상의 이유로 고객 이름 변경
		memberService.biz(); // 비즈니스 로직
		model.addAttribute("member", member");
	}
}
```

위 문제의 원인은, 같은 영속성 컨텍스트를 여러 트랜잭션이 공유할 수 있기 때문에 발생한다. OSIV 사용하지 않는 Transaction 범위의 영속성 컨텍스트 전략은 트랜잭션의 생명주기와 영속성 컨텍스트의 생명주기가 같으므로 이런 문제가 발생하지 않는다.

## References

[자바 ORM 표준 JPA 프로그래밍](https://www.aladin.co.kr/shop/wproduct.aspx?itemid=62681446)
