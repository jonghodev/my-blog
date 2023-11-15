---
title: C
date: 2020-12-12 19:12:80
update: 2020-12-12 19:12:80
tags:
 - development
description:
---

## 컴파일 / 링킹

소스 코드를 실행 가능한 파일로 만들기 위해 컴파일링과 링킹을 이라는 두 단계를 거친다. 컴파일러는 소스 코드를 중간 단계의 코드로 변환시키며, 링커는 실행 파일을 만들기 위해 다른 코드(시스템 라이브러리 파일과 중간 단계에서 생성되는 오브젝트 코드)를 결합해 주는 역할을 한다.

C는 모듈화가 가능하게 하기 위해 각각의 모듈을 따로 컴파일 한 후, 컴파일 된 모듈들을 링커로 합칠 수 있다. 따라서, 전체 프로그램 중 특정한 모듈만 수정한 경우 나머지 모듈을은 다시 컴파일 할 필요가 없다.

## Standard I/O

- getchar()
- scanf()
- gets()

위 함수는 Buffer 에서 데이터를 Read 한다는 (Buffered I/O) 점에선 동일하다.

대신 `getchar()` 는 1 개의 character 만 int 형식으로 반환하고 `scanf()` 는 %d 와 같은 서식 문자열을 주어서 원하는 값으로 파싱해도 들고오고 `gets()` 는 한 라인을 들고오는 것 뿐이다.

반면, `conio.h` 에서 들고오는 `getch()`[https://www.geeksforgeeks.org/getch-function-in-c-with-examples/] 함수의 경우 buffer 를 사용하지 않고 keyboard 의 입력을 waitng 하다가 인터럽트가 발생하면 즉시 그 문자를 가져오는 방식이다.

> gets() 함수는 심각한 보안 결함이 있어서 gets_s() 를 사용하는 것이 좋다.

> scanf() 또한 문제가 있는데 scanf_s() 함수를 사용하면 된다. 하지만 윈도우에만 내장되어 있다.

## 컴파일 최적화

> 의존성이 없는 소스코드는 동시에 실행해도 된다.

> 컴파일러는 논리적으로 매우 당연한 코드는 제거한다.

debug mode build 에서는 잘 작동하나, release mode build 에서는 오 작동하는 경우가 있다.

우리가 하이 래벨 코드 상에서 최적화 해서 짜는 건 어렵다. 그리고 최적화는 로우 래벨의 범위에서 일어나야 한다.

그래서 우리는 **컴파일러가 최적화하기 좋은 코드**를 작성하고, 컴파일러가 최적화를 하게 맡긴다.

컴파일러가 최적화하기 좋은 코드는 다음과 같다. -> 최적화 방해요소를 제거하라.

1. 변수 사용을 줄인다. -> 논리적 복잡성 제거
2. 포인터 사용 주의 -> 포인터는 런타임 시점에 결정되기 때문에 논리적 복잡성 증가. (c++ 에서는 참조 사용)

const 는 유지보수면에서도 좋지만, 변수가 아니라 상수로 인식되기 때문에 성능상 더 좋다. (변수가 줄어듬.)

> 고수들은 const 를 더 자주 사용한다고 한다.

### Macro

함수를 호출(Call)하면 매개 변수가 복사되고 스택도 쌓인다. 이것을 Call Overhead 라고 한다. (따라서 크기가 큰 구조체 같은 것을 함수에 넘기지 않는게 좋다.)

함수로 만들기에 내용이 작고 자주 호출되는 것은 Macro 로 사용해서 Call Overhead 를 없앤다.

### Inline

컴파일 옵션에서 Inline 확장을 사용하면, 컴파일이 어떤 코드를 보고 판단하기에 인라인으로 처리할만하다고 생각하면 매크로처럼 번역이 된다. (Release build 의 경우)

그래서 함수를 호출해도 Inline 으로 처리되기 때문에 함수 콜 스택이 사용되지 않고 성능이 더 좋아지게 된다.

### Preprocessor If expression

실무에서는 다음과 같이 사용할 수 있다.

```c
#ifdef _DEBUG
  printf("debug mode")
#else
  printf("release mode")
```

### Macro 안 좋은 이유

Macro 함수에서 자료형을 검사하지 않는다. 그래서 Compile 시점에서 Warning 을 알 수 없다. 또한 연산자 순서에 의한 문제도 발생할 수 있다.

```c
#define ADD(a, b) (a + b)

int main(void)
{
  printf("%d\n", ADD(3, 4.5)); // 문제가 있는 코드
  return 0;
}
```

Macro 를 사용하게 된 배경은 성능 향상, 함수 호출의 오버헤드를 줄이기 위해 사용하게 된 것이다.

그런데 `inline` 이 그것을 대체할 수 있으므로 되도록 Inline 사용하돼 Inline 으로 구현이 힘든 것은 매크로를 사용하면 된다.

그러므로`inline`, `const` 를 사용하는 것이 좋다.

> C++ 에는 inline 이 있지만 Java, C# 에는 없다. [참고](https://stackoverflow.com/questions/2096361/are-there-inline-functions-in-java)
