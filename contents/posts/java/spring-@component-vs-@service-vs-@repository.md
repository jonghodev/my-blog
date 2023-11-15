---
title: Spring @Component vs @Service vs @Repository
date: 2021-06-02 20:00:00
update: 2021-06-02 20:00:00
tags:
 - java
description:
---

## 요약

- @Component: 스프링에 의해서 관리되는 일반적인 컴포넌트.
- @Service: Service Layer 임을 명시
- @Repository: Persistence Layer 임을 명시

## 차이

### @Component

@Repository, @Service Annotation 코드를 보면 아래와 같이 선언되 있다.

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Repository {

	/**
	 * The value may indicate a suggestion for a logical component name,
	 * to be turned into a Spring bean in case of an autodetected component.
	 * @return the suggested component name, if any (or empty String otherwise)
	 */
	@AliasFor(annotation = Component.class)
	String value() default "";

}

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Service {

	/**
	 * The value may indicate a suggestion for a logical component name,
	 * to be turned into a Spring bean in case of an autodetected component.
	 * @return the suggested component name, if any (or empty String otherwise)
	 */
	@AliasFor(annotation = Component.class)
	String value() default "";

}
```

@Service 와 @Repositry 둘 모두 @Component 를 사용하고 있어서, 상속 개념으로 봐도 괜찮을 것 같다.

### @Repository

Persistence 에 관한 예외가 생겼을 때, Spring 이 관리하는 unchecked exception 으로 다시 예외를 던진다.

### @Service

@Repositry 와 달리 더 추가적인 기능은 없고, 단지 비즈니스 로직을 담고 있는 서비스 레이어임을 명시한다.

코드 가독성을 위해 비즈니스 로직이 포함된 서비스 레이어일 경우 @Service 어노테이션과 @Component 를 구분해서 사용하는게 좋다.

## References

https://www.baeldung.com/spring-component-repository-service
