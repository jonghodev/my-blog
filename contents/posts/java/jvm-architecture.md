---
title: JVM Architecture
date: 2021-06-11 22:00:00
update: 2021-06-11 22:00:00
tags:
 - java
description:
---

## 소개

JVM 에 대해 알아보자. JVM 을 이해하는 것은 자바가 어떻게 동작하는지 아는데 중요하다.

또한 GC 는 성능, 보안, 안정성, 신뢰성이 크게 중요하지 않은 시스템에선 크게 신경쓸 필요 없지만, 성능이 중요하고 Mission Critical 한 시스템에선 중요하다.

## 목차

- Java Environments
- How Java Works
- JVM Architecture
  - Cloass Loader Subsystem
    - Class Loader
  - Runtime Data Area
    - Method Area
    - Heap Area
    - Stack Area
    - PC Register
    - Native Stack Area
  - Execution Engine
    - Interpretor
    - Just-In-Time (JIT) Compiler
    - GC
  - Java Native Interface (JNI)
  - Native Method Libraries
- Threads
- Some Pointers for Understanding

## Java Environments

JDK (Java Development Kit) = JRE (Java Runtime Enviroment) + Development Tool

JRE 는 자바 프로그램을 실행하기 위한 소프트웨어이며 JVM 이 내장되어 있다.

JDK 는 JRE 를 포함하며, 자바 개발자가 개발하기 위해 사용하는 소프트웨어다.

## How Java Works

자가 개발자가 소스를 작성한다. 이때 확장자는 .java 로 Member.java 처럼 생성한다.

.java 소스를 자바 컴파일러가 컴파일하면 Member.class 같은 .class 바이트코드가 생성된다. 바이트코드는 16진수로 되어있다.

자바 소스를 컴파일할 때는 javac 를 사용하는데 이것은 JDK 안에 내장되어 있다.

C / C++ 같은 언어는 특정 플랫폼에 맞추어서 컴파일을 하면 그 OS 에서만 가능하다.

반면 자바는 WORA 라는 개념을 따르고 있다. WORA: "Write once, run anywhere"

컴파일된 바이트코드를 JVM 에 올리면 JVM 이 Native Machine Language 로 변환한다. 이 Native Machine Language 는 그 하드웨어의 OS 가 바로 읽을 수 있는 언어이다. JVM 이 그 OS 에 맞추어서 바이트 코드를 해석해주니 **OS 에 관계 없이(platform-independant, portable)** 실행할 수 있다.

따라서 자바 프로그램을 실행하는 OS 는 적절한 JVM, JRE 를 설치할 필요가 있다.

## JVM Architecture

![](./images/JVM.png)

## Cloass Loader Subsystem

JVM 은 Ram 에 존재하는데 Class Loader 가 클래스 파일을 램의 Runtime Data Area 로 적재한다. 이것은 자바의 **Dynamic class loading** 이라고 한다.

Class loader 는 Loading, Linking, Initilization 을 한다.

### Loading

메인 클래스를 시작으로 로딩 작업을 시작한다.

### 1. Class Loader

## Runtime Data Area

### 1. Method Area (Shared among Threads)

JVM 당 하나만 존재하며 쓰레드 간에 공유되는 자원이다. 메소드 영역에는 다음 데이터들을 저장한다.

- Classloader reference
- Runtime constant pool: 숫자 변수, 필드 참조, 메소드 참조
- Field data: 멤버 변수의 이름, 타입
- Method data - 메소드의 이름, 반환 타입, 파라미터 타입, 순서
- Method code - 메소드의 바이트 코드, 스택 크기, 지역 변수 크기

### 2. Heap Area (Shared among Threads)

이곳도 또한 Method Area 와 마찬가지로 한개만 존재하며 쓰레드 간에 공유가 된다. 그래서 쓰레드 간의 자원 공유, 동시성 문제를 염두에 둬야한다. 객체가 생성이 되면 이곳에 저장이 되고 GC 의 대상이 되는 유일한 영역이다.

### 3. Stack Area (Per Thread)

스레드가 시작할 때 할당되어 스레드 당 한 개씩 갖고 있다. 스레드간 공유 되지 않는 자원이다.

**Stack Frame**: 메소드 호출이 일어날 때마다 프레임에 Push 되며 종료되면 Pop 한다.

메소드 정보, 지역변수, 매개변수, 연산 중 발생하는 임시 데이터를 저장한다.

기본 타입 변수는 스택 영역에 직접 값을 갖고, 참조 타입 변수는 힙 영역에 있는 객체의 주소를 갖는다.

스레드가 종료되면 스텍 프레임도 JVM 에 의해 제거 된다.

스택의 크기는 고정될 수도 있고 동적으로 바뀔 수도 있다. 만약 스레드가 허용된 범위보다 많은 스텍을 요구하면 StackOverFlowError 가 생기고, 새로운 프레임을 요구할 때 메모리가 충분하지 않다면 OutOfMemoryError 가 생긴다.

### 4. PC Register (Per Thread)

현재 실행 중인 코드의 주소를 기억한다. 이렇게 스레드 마다 자신이 실행하고 있는 주소를 기억할 수 있어서 여러 스레드가 돌아가면서 실행이 가능한 것이다.

### 5. Native Stack Area (Per Thread)

자바 스레드와 Host OS 스레드와의 direct 로 연결된 Mapping 이 있다. 자바 스레드를 위한 모든 상태를 준비하고 Native Stack Area 가 생성된다.

JNI(Java Native Interface) 로 실행되는 Native Method 정보를 저장하기 위한 공간이다. 보통 Native Method 는 C/C++ 로 작성된다.

## Execution Engine

실질적으로 바이트코드를 읽어 실행하는 곳이다.

Runtime Data Areas 에 할당된 바이트코드을 한줄씩 읽어 실행한다.

### Interpretor

바이트코드를 해석하고 실행한다. 따라서 해석하는 작업은 빠르지만 실행하는 것은 느리다.

Interpretor 의 단점은 똑같은 메소드를 여러 번 호출할 때 느리고 비효율적이란 것이다. 같은 메소드를 실행해도 처음 보는 것처럼 해석을 하고 실행할 때는 속도까지 느리기 때문이다.

### Just-In-Time (JIT) Compiler

위에서 언급한 Interpretor 의 단점을 해결해준다. 한 개의 메소드가 여러 번 호출되는 것을 감지해서, 그 바이트코드를 컴파일해서 Native Code (Mahcine Code) 로 변경하고 Cache 에 저장한다.

미리 Native code 로 변환된 코드를 사용하기 때문에 Interpretor 보다 속도가 빠르다.

하지만 Interpretor 보다 코드를 해석하는데 걸리는 시간은 더 오래걸린다. 그리고 한 번만 실행할 것이라면 Interpretor 가 더 효율적이며, Cache 에 Native Code 를 저장하는 것도 비싼 작업이라는 점을 감안해야 한다.

그래서 특정 횟수 이상 여러 번 호출되는 메소드를 내부적으로 검사해서 컴파일을 하는 작업을 한다. 이것은 **adaptive compiling**의 개념이며, Oracle Hospot VMs 에서도 사용된다.

> Javascript 를 완전 Interpretor 언어로 오해하는 사람들이 있다. 하지만 사실은 JIT 기반으로 동작하여 자주 사용되는 코드는 컴파일해서 캐싱해서 사용한다.

### GC

GC 는 처음에 LISP 라는 어너에서 처음 도입된 개념으로 자바가 이 개념을 사용하므로써 대중화 되었다.

프로그래머는 자유롭게 힙을 사용하고, 더이상 사용되지 않은 힙 안에 있는 객체는 GC 가 자동으로 제거해준다.

C, C++ 에서는 OS 레벨의 메모리에 접근하기 때문에 `free()` 함수를 호출해서 메모리 할당을 명시적으로 해제해줘야 한다. 그렇지 않으면 memory leak 이 발생하게 된다.

반면, 자바는 OS 영역의 메모리에 접근하지 않고 GC 가 이를 대신해서 수행해준다. 객체가 필요하지 않은 시점에 알아서 `free()` 함수를 호출해준다. 우리가 모든 기능을 개발하지 않고, 이미 검증되며 테스트된 라이브러리를 사용하듯이 메모리 관리라는 까다로운 부분을 GC 에게 맡기는 것이다.

만약 메모리 할당 해제를 명시적으로 처리하고 싶으면 System.gc() 를 사용할 수 있지만 성능에 영향이 많이가는 메소드인 만큼 매우 신중히 사용해야 한다.

JVM 은 실행 시 옵션을 주어서 미리 지정한 메모리를 할당해서 실행한다. 그리고 그 할당된 메모리를 초과하면 `Out of memory` 에러가 나면서 자동으로 프로그램으로 종료된다. **JVM 안에서만 메모리 누수가 발생하므로 OS 래벨의 memory leak 은 발생하지 않는다.**

Memory Configuration

- -XmsSetting: initial Heap size
- -XmxSetting: max Heap size

## Java Native Interface (JNI)

Native Method Library 와의 상호작용을 위해 필요하다. JVM 에서 Native Method 를 호출할 수도 있고, 호출이 될 수도 있다.

## Native Method Libraries

Native Library 는 일반적으로 C / C++ 으로 작성된다.

## Threads

자바에서는 두 종류의 스레드가 있다. 하나는 JVM 에 의해서 생성되는 Background 작업을 하는 system threads, 나머지는 우리가 코딩한 프로그램이 실행되는 application threads 다.

자바 프로그램이 실행되면 **main thread**는 **public static void main(String[])** 메소드를 실행하면서 실행된다.

system threads 는 다음과 같다.

- Compiler threads: 바이트코드를 Native 코드로 컴파일한다.
- GC threads: GC 관련한 모든 작업을 수행한다.
- Periodic task thread: Timer 이벤트 같은 주기적으로 일어나는 작업을 처리한다.
- Signal dispatcher thread
- VM thread

## Some Pointers for Understanding

- 자바는 동시에 Interpreterd, Compiled language 속성을 갖고 있다.
- 자바는 속도가 느리다. Runtime 중에 Dynamic linking 과 interpreting 을 하기 때문이다.
- JVM 은 단순히 명세(specification)에 불과하다. Vendors 들은 자유롭게 커스터마이징하고 성능을 향상시키곤 한다. 벤더에는 Oracle, IBM 과 같은 업체들이 있다.

### References

https://d2.naver.com/helloworld/1329

https://12bme.tistory.com/57

https://hoonmaro.tistory.com/19

https://medium.com/platform-engineer/understanding-jvm-architecture-22c0ddf09722

https://yaboong.github.io/java/2018/06/09/java-garbage-collection/

https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-5.html
