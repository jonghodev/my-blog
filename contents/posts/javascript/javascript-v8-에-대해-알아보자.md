---
title: Javascript V8 에 대해 알아보자
date: 2020-11-15 17:11:03
update: 2020-11-15 17:11:03
tags:
 - javascript
description:
---

크롬 브라우저는 **Blink** 라는 **Renderer** 엔진 (html, css)과 Javascript 의 V8 엔진(javascript) 을 가지고 있다.

## V8 의 특징

V8 엔진은 C++ 로 작성되었다.

대표적으로 V8 엔진은 은 크롬 브라우저와 Node.js 에서 이용된다.

V8 은 아래 특징을 갖는다.

- Javascript 소스 코드를 컴파일 하고, 실행한다.
- 생성하는 Object 를 메모리에 할당한다.
- Garbage Collection 을 사용해 더 이상 사용하지 않는 Object 의 메모리를 해제한다.
- Hidden Class 를 이용해 빠르게 프로퍼티에 접근한다.
- TurboFan 을 이용해 최적화된 코드로 만들어 속도 및 메모리를 최적화한다.

## JIT Compiler

Javascript Interpreter 방식으로 작동한다고 알려져 있다. 왜냐면 컴파일 과정 없이 바로 한줄 한줄 실행되기 때문이다.

그렇지만 내부적으로 V8 은 항상 Interperet 하지 않는 구조로 되어있다. 왜냐면 똑같은 코드가 올 때마다 컴파일하는건 비효율적이기 때문이다.

그래서 JIT(Just-In-Time) 라는 방식을 사용한다.

V8 은 우선 Javascript 코드을 Interpreter 로 컴파일 한 후 이것을 Byte Code 로 만들어 낸다. 그 후 이 Byte Code 를 캐싱해두고, 자주 쓰이는 코드를 인라인 캐싱과 같은 최적화 기법을 사용해 최적화 한다.

그리고 이후에 Compile 할 때 참고하여 캐싱 값을 불러오는 방식을 사용한다.

## References

https://oowgnoj.dev/review/js-remember

https://blog.sessionstack.com/how-javascript-works-parsing-abstract-syntax-trees-asts-5-tips-on-how-to-minimize-parse-time-abfcf7e8a0c8

https://oowgnoj.dev/review/advanced-js-1

https://mathiasbynens.be/notes/shapes-ics

https://medium.com/@pks2974/v8-%EC%97%90%EC%84%9C-javascript-%EC%BD%94%EB%93%9C%EB%A5%BC-%EC%8B%A4%ED%96%89%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-%EC%A0%95%EB%A6%AC%ED%95%B4%EB%B3%B4%EA%B8%B0-25837f61f551
