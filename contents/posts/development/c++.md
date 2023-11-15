---
title: C++
date: 2020-12-12 09:12:74
update: 2020-12-12 09:12:74
tags:
 - development
description:
---

## ASLR

OS 는 ASLR(Address Space Layout Randomization) 을 지원한다.

VS 에서 위 옵션을 사용할지 말지 설정할 수 있다.

## Name Mangling

```cpp
#include <iostream>

int Add(int a, int b);
double Add(double a, double b);

int _tmain(int argc, _TCHAR* argv[])
{
  Add(3,4);
  Add(3.3, 4.4);
  return 0;
}

int Add(int a, int b)
{
  return a + b;
}

double Add(double a, double b)
{
  return a + b;
}
```

C++ 에서 함수를 생성하고 컴파일을 하면 실제론 다른 이름이 생긴다. (mangling)

근대 다른 프로그램에서 이 DLL 을 호출할 때 문제가 생길 수 있다.

```cpp
#include <iostream>

extern "C"
{
  int Add(int a, int b);
  double Add(double a, double b);
}

int _tmain(int argc, _TCHAR* argv[])
{
  Add(3,4);
  Add(3.3, 4.4);
  return 0;
}
```

위 코드와 같이, extern "C" 를 사용하면 컴파일 할 때 mangling 을 사용하지 않고 Add 라는 함수 이름을 사용하게 된다.
