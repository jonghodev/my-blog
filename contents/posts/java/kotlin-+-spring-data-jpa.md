---
title: Kotlin + Spring Data JPA
date: 2021-06-04 18:00:00
update: 2021-06-04 18:00:00
tags:
 - java
description:
---

## findByIdOrNull

```kotlin
import org.springframework.data.repository.findByIdOrNull

val user: User? = userRepository.findByIdOrNull(1)
user?.username ?: ""
```

Kotlin extension function 으로 구현되어 있어 import 가 필요하다.

## Java Entity To Kotlin Entity

```kotlin
@Entity
class Person {
    @Id
    @GeneratedValue
    var id: Long? = null

    @Column(nullable = false)
    var name: String = ""

    var phoneNumber: String? = null
}
```

## Named Arguments

Java 에서는 아래와 같이 사용하지만

```java
val person = Person()
person.id = 1
person.name = "jongho"
person.phoneNumber = "010-1234-1234"
```

Kotlin 에서는 다음과 같이, 파라미터에 이름을 주어서 사용할 수 있다.

```kotlin
val person = Person(
  id = 1,
  name = "jongho",
  phoneNumber = "010-1234-1234"
)
```

## Primary Constructor

아래와 같이 생성자를 만들 필요성이 있을 때

```kotlin
@Entity
class Person {
    @Id
    @GeneratedValue
    var id: Long? = null

    @Column(nullable = false)
    var name: String = ""

    var phoneNumber: String? = null

    constructor(id: Long?, name: String, phoneNumber: String?) {
      this.id = id
      this.name = name
      this.phoneNumber = phoneNumber
    }
}
```

코틀린에서는 다음과 같이 프로퍼티 뒤에 콤마를 주어서 간단하게 해결이 가능하다.

```kotlin
@Entity
class Person {
    @Id
    @GeneratedValue
    var id: Long? = null,

    @Column(nullable = false)
    var name: String = "",

    var phoneNumber: String? = null
}
```

## Lazy Loading

Member 엔티티가 Person 엔티티를 Lazy Loading 속성으로 들고있다고 해보자.

```kotlin
val member = memberRepository.findByIdOrNull(1)
val person = member.person
println(person::class)
```

위와 같이 조회하고 Lazy Loading 된 person 엔티티를 출력하면 우리는 보통 com.example.Person$HibernateProxy$WQfWq 와 같이 JPA 구현체의 Proxy 객체가 나올 것으로 예상한다.

하지만 실제로는 Person 클래스 자체가 나온다.

그 이유는 기본적으로 Kotlin 에서 클래스는 final 이기 때문에 Proxy 객체가 Person 객체를 상속할 수 없기 때문이다. 따라서 클래스 선언부에 `open` 이라는 키워드를 주고 프로퍼티에도 `open` 키워드를 줘야한다. 다음과 같이 말이다.

> Java 기본 문법: Final 로 선언된 클래스는 상속할 수 없다.

```kotlin
@Entity
open class Person {
    @Id
    @GeneratedValue
    open var id: Long? = null,

    @Column(nullable = false)
    open var name: String = "",

    open var phoneNumber: String? = null
}
```

하지만 위 코드는 open 이라는 필드를 일일이 주어야하는 번거로움이 있어서 Kotlin 컴파일러 플러그인으로 이를 손쉽게 대체할 수 있다.

Gradle 플러그인에 아래의 플러그인을 추가해주자.

```gradle
plugins {
    ...
    kotlin("plugin.allopen") version "1.5.20"
}

allOpen {
    annotation("javax.persistence.Entity")
}
```

위 코드는 @Entity 가 붙은 클래스와 그 클래스의 멤버를 자동으로 open 으로 만들어주는 기능이다.

## No Arg Plugin

JPA 에서는, 엔티티 클래스에서 기본적으로 파라미터가 없는 기본 생성자를 필요로 한다.

Kotlin 컴파일러를 이용하면 이 기본 생성자도 특정 어노테이션이 붙어 있는 클래스에 자동으로 만들어준다.

다음과 같이 적용해준다.

```gradle
plugins {
    ...
    kotlin("plugin.noarg") version "1.5.20"
}

noArg {
    annotation("javax.persistence.Entity")
}
```

## References

https://www.youtube.com/watch?v=Ou_-DFaAUhQ&list=PLdHtZnJh1KdaM0AfxPA7qGK1UuvhpvffL&index=7

https://blog.junu.dev/37
