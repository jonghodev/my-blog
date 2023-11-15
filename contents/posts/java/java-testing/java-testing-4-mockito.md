---
title: Java Testing 4 - Mockito
date: 2021-06-20 22:00:00
update: 2021-06-20 22:00:00
tags:
 - java
description:
---

## 1. 소개

Mock: 진짜 객체와 비슷하게 동작하지만 프로그래머가 직접 그 객체의 행동을 관리하는 객체.

[Mockito](https://site.mockito.org/): Mock 객체를 쉽게 만들고 관리하고 검증할 수 있는 방법을 제공한다.

## 2. 시작

스프링부트 2.2+ 프로젝트 생성 시 spring-boot-starter-test 에서 자동으로 추가해줍니다.

### 스프링부트를 쓰지 않는다면?

Mockito Core와 JUnit 연동과 관련된 도구를 설치해야 합니다.

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>3.1.0</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-junit-jupiter</artifactId>
    <version>3.1.0</version>
    <scope>test</scope>
</dependency>
```

## 3. Mock 객체 만들기

### 3-1. Mockito.mock() 메소드로 만들기

```java
MemberService memberService = mock(MemberService.class);
StudyRepository studyRepository = mock(StudyRepository.class);
```

### 3-2. @Mock 애노테이션으로 만들기

JUnit 5 extension 으로 MockitoExtension을 사용해야 한다.

- 필드에서 사용할 수 있고
- 메소드 매개변수에서 사용할 수 있다.

```java
@ExtendWith(MockitoExtension.class)
class StudyServiceTest {

    @Mock MemberService memberService;

    @Mock StudyRepository studyRepository;
}
```

```java
@ExtendWith(MockitoExtension.class)
class StudyServiceTest {

    @Test
    void createStudyService(@Mock MemberService memberService,
                            @Mock StudyRepository studyRepository) {
        StudyService studyService = new StudyService(memberService, studyRepository);
        assertNotNull(studyService);
    }

}
```

## 4. Mock 객체 Stubbing

모든 Mock 객체의 행동

- Null을 리턴한다. (Optional 타입은 Optional.empty 리턴)
- Primitive 타입은 기본 Primitive 값.
- 콜렉션은 비어있는 콜렉션.
- Void 메소드는 예외를 던지지 않고 아무런 일도 발생하지 않는다.

Mock 객체를 조작해서

- 특정한 매개변수를 받은 경우 특정한 값을 리턴하거나 예뢰를 던지도록 만들 수 있다.
  - [How about some stubbing?](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#2)
  - [Argument matchers](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#3)
- Void 메소드 특정 매개변수를 받거나 호출된 경우 예외를 발생 시킬 수 있다.
  - [Subbing void methods with exceptions](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#5)
- 메소드가 동일한 매개변수로 여러번 호출될 때 각기 다르게 행동하도록 조작할 수도 있다.
  - [Stubbing consecutive calls](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#10)

```java
@ExtendWith(MockitoExtension.class)
class StudyServiceTest {

    @Test
    void createStudyService(@Mock MemberService memberService,
                            @Mock StudyRepository studyRepository) {
        StudyService studyService = new StudyService(memberService, studyRepository);
        assertNotNull(studyService);

				Member member = new Member();
				member.setId(1L);
				member.setEmail("jongho.dev@gmail.com");

				// 한 번 호출 되면 member 객체 return
				// 두 번 호출 되면 Exception Throw
				// 세 번 호출 되면 null return
				when(memberService.findById(any()))
						.thenReturn(member)
						.thenThrow(new RuntimeException())
						.thenReturn(null);

				assertEquals("jongho.dev@gmail.com". memberService.findById(1L).getEmail();

				assertThrows(RuntimeException.class, () => {
						memberService.findById(2L);
				});
    }

}
```

## 5. Mock 객체 확인

Mock 객체가 어떻게 사용이 됐는지 확인할 수 있다.

- 특정 메소드가 특정 매개변수로 몇번 호출 되었는지, 최소 한번은 호출 됐는지, 전혀 호출되지 않았는지
  - [Verifying exact number of invocations](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#exact_verification)
- 어떤 순서대로 호출했는지
  - [Verification in order](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#in_order_verification)
- 특정 시간 이내에 호출됐는지
  - [Verification with timeout](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#verification_timeout)
- 특정 시점 이후에 아무 일도 벌어지지 않았는지

```java
verify(memberService, times(1)).findById(study)
```

## 6. Mockito BDD 스타일 API

[BDD](https://en.wikipedia.org/wiki/Behavior-driven_development): **애플리케이션이 어떻게 “행동”해야 하는지**에 대한 공통된 이해를 구성하는 방법으로, TDD에서 창안했다.

행동에 대한 스팩

- Title
- Narrative
  - As a / I want / so that
- Acceptance criteria
  - Given / When / Then

Mockito는 BddMockito라는 클래스를 통해 BDD 스타일의 API를 제공한다.

When -> Given

given(memberService.findById(1L)).willReturn(Optional.of(member));

given(studyRepository.save(study)).willReturn(study);

Verify -> Then

then(memberService).should(times(1)).notify(study);

then(memberService).shouldHaveNoMoreInteractions();

참고

- [https://javadoc.io/static/org.mockito/mockito-core/3.2.0/org/mockito/BDDMockito.html](https://javadoc.io/static/org.mockito/mockito-core/3.2.0/org/mockito/BDDMockito.html)
- [https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#BDD_behavior_verification](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#BDD_behavior_verification)
