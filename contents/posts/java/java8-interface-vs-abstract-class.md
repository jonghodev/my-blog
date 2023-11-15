---
title: JAVA8 interface vs abstract class
date: 2021-06-04 18:00:00
update: 2021-06-04 18:00:00
tags:
 - java
description:
---

## 인터페이스

인터페이스란 상수(static final)와 추상 메소드(abstract method)의 집합이다. 인터페이스에 선언된 상수와 메소드는, 컴파일러가 자동으로 `public static final` 과 `pulbic abstract` 을 생성해준다. 생성자를 가질 수 없어서 객체화가 불가능하고, 다중 상속을 지원한다.

인터페이스를 이해하기 위해선 `has` 의 개념으로 보는 것이 좋다. 개, 잉어, 물개 라는 클래스 모두 Swimaable 인터페이스를 구현할 수 있다.

## 추상클래스

추상클래스는 필드, 생성자, 추상메소드를 가질 수 있다. 생설자를 가지기 때문에 객체화가 가능하며 인터페이스와 다르게 상수가 아닌 필드도 가질 수 있다.

추상클래스를 이해하기 좋은 영어 숙어는 `is a kind of` 이다. "사람은 동물의 한 종류이다.", "사자는 포유류의 한 종류이다." 와 같이 상위 하위 개념으로 보는 것이 좋다.

## 추상클래스, 인터페이스의 차이

### 접근제어자

인터페이스에 모든 변수는 기본적으로 `public static final` 이며, 모든 메소드는 public abstract 이다.

반면 추상클래스에서는 public, protected, private, static, final 키워드를 사용할 수 있다.

### 다중 상속

인터페이스는 다중 상속이 되지만, 추상클래스는 다중 상속이 안 된다.

## 추상클래스, 인터페이스의 적절한 사용 케이스

### 추상클래스

- 좀 더 상속의 개념으로 봐야 해서, 관련성이 높은 코드끼리 코드를 공유하고 싶은 경우
- 상속받는 클래스들이 공통으로 가지는 메소드와 필드가 많을 경우
- public 이외의 접근 제어자 사용이 필요할 경우

### 인터페이스

- 어떤 행동을 명시하는 것이므로, 서로 관련성이 없는 클래스들이 인터페이스를 구현할 때 사용한다. 예를들어 `Comparable`, `Cloneable` 인터페이스는 여러 클래스에서 구현되는데, 그 구현클래스들 간에 관련성이 없는 경우가 많다.
- 다중 상속 허용을 위해

## References

https://yaboong.github.io/java/2018/09/25/interface-vs-abstract-in-java8/

https://myjamong.tistory.com/150#:~:text=%EC%B6%94%EC%83%81%ED%81%B4%EB%9E%98%EC%8A%A4%EA%B0%80%20%EB%AF%B8%EC%99%84%EC%84%B1%20%EC%84%A4%EA%B3%84%EB%8F%84,(%EA%B5%AC%ED%98%84)%EC%9D%B4%20%EA%B0%80%EB%8A%A5%ED%95%A9%EB%8B%88%EB%8B%A4.

```

```
