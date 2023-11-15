---
title: kotlin
date: 2020-11-28 22:50:00
update: 2020-11-28 22:50:00
tags:
 - development
description:
---

코틀린으로 직성한 소스코드는 자바의 class 파일로 바뀌어서 JVM 에서 실행한다.

코틀린은 개발 생산성과 유지보수성, 그리고 최신 프로그래밍 패러다임을 쉽게 제공한다.

코틀린이 지원하는 것

### Null 안전성

Null 에 대한 다양한 처리 기법을 제공하므로 Null 때문에 발생하는 문제을 쉽게 작성할 수 있다.

### 확장 함수

최근 프로그래밍 언어들의 트랜드처럼 상속을 통하지 않고 함수 확장(Extension)을 통해 기존 클래스의 기능을 쉽게 추가할 수 있다.

### 함수형 프로그래밍

코틀린은 람다 식이나 고차 함수를 제공한다.

### data 클래스

vo 를 위한 data class 를 제공한다.

### 지원하는 기능

세미클론을 입력하지 않아도 된다.

최상위 구성 요소가 클래스가 아니어도 된다.

## 변수

val 키워드로 선언된 변수는 상수라고 할 수 없다. 왜냐면 kotlin 에서 변수는 property 이기 때문이다. val 키워드로 선언된 변수는 읽기 전용 변수라고 할 수 있다.만약 get() property 를 주면 읽을 때마다 다른 값을 읽을 수 있기 때문이다.

const 키워드는 처음에 할당한 값을 읽을 수 있는 상수의 개념이다. var 와 쓰일 수 없고 val 키워드와 사용되야 하며 `const val value = 1` 과 같이 사용된다. const 키워드를 사용할 수 있는 곳은 변수를 최상위 래밸로 선언하거나 object 로 선언한 클래스에서만 사용할 수 있다.

그 이유는 아마, 클래스의 객체별로 데이터를 다르게 표현할 수도 없는데 클래스 내부에 선언하는 것이 불필요하기 때문인 것 같다. 차라리 최상단 레벨로 선언하여 전역변수처럼 이용되는 것으로 충분할 것 같다.

예제는 다음과 같다.

```kotlin
const val myConst1: Int = 10;
const var myConst2: Int = 10; // Error

class MyClass {
	const val myConst3 = 30 // Error
}
fun some() {
	const val myConst4 =40 // Error
}
```

## 함수

함수를 선언할 때 매개변수는 val, var 를 사용할 수 없다, 매개변수는 무조건 val 로 선언이 적용이 된다. 즉 함수 내부에서 매개변수를 수정할 수 없다는 의미다.
함수에서 반환값이 없을 때는 Unit 을 사용하면 된다. 그리고 생략이 가능하다.
코틀린에서는 함수에서 할수를 정의할 수도 있다.

파라미터를 다르게하여 오버로딩이 가능하다.

default argument 를 정의할 수 있다.

### 중의 표현식(infix)

```kotlin
Infix fun Int.myFun(x: Int): Int = println(x * x)
class myClass {
	infix fun infixFun(a: Int) = println(x * x)
}

Infix fun Int.myFun(x: Int): Int = x * x

fun main(args: Array<String>) {
	val obj = myClass()
obj.infixFun(2)
	obj infixFun 2
	10.myFun(10)
	10 myFun 10
}
```

다음의 경우에만 사용이 가능하다.

- 클래스의 멤버 함수로 선언하거나 클래스의 확장 함수일 때
- 매개변수가 하나인 함수일 때

## References

깡쌤의 코틀린

https://www.slideshare.net/ifkakao/ss-113145569
