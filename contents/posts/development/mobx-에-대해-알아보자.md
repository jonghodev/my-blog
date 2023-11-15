---
title: Mobx 에 대해 알아보자
date: 2020-11-16 06:40:93
update: 2020-11-16 06:40:93
tags:
 - development
description:
---

React 의 상태 관리 라이브러리는 대표적으로 Redux 와 Mobx 가 있다.

Redux 는 Flux 라는 패턴을 따라간다.

Mobx 에 대해 알아보자.

## Mobx 란

## Mobx 의 장점

### 객체지향적이다.

클래스를 활용해 도메인간의 상호작용을 message 를 통해 주고받는 방향으로 구현할 수 있다.

### Decorator

Java 에서 말하는 Annotation 과 유사한 Decorator 를 사용해 코드를 깔끔하게 유지한다.

### 캡슐화

Mobx Configuration 설정으로 State 를 오직 함수를 통해서만 변경하며 Private 관리할 수 있다.

### 불변성 유지를 위한 노력 불필요

Redux 처럼 불변성을 유지하기 위한 노력을 할 필요가 없다.

> ex) 순수함수, ImmutableJs 러닝커브

## References

https://woowabros.github.io/experience/2019/01/02/kimcj-react-mobx.html
