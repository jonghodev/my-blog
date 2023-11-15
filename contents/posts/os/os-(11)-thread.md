---
title: OS (11) Thread
date: 2020-11-26 12:37:00
update: 2020-11-26 12:37:00
tags:
 - os
description:
---

### 쓰레드

쓰레드란 프로세스 내에서 실행되는 흐름의 단위다. 쓰레드마다 다른 실행 흐름을 가지기 때문에 각자의 PC, SP, 레지스터, 스택을 갖으며 메모리는 공유한다.

쓰레드를 활용해 의존성이 없는 작업을 동시에 실행시켜 처리 능력을 향상 시킬 수 있다.

![](./images/2020-11-26-thread.png)

### 다중 쓰레드

- 한 프로그램에 2개 이상의 맥
- 맥이 빠른 시간 간격으로 스위칭 된다. => 여러 맥이 동시에 실행되는 것처럼 보인다.

Thread 도 Process의 시분할처럼 여러 번 스위칭되어서 동시에 동작하는 것처럼 보인다.

### Single thread vs Multi thread

단일 쓰레드 (single thread) 프로그램: 한 프로세스에 기본 1개의 쓰레드가 있다.

다중 쓰레드 (multi thread) 프로그램: 한 프로세스에 여러 개의 쓰레드가 있다.

현대 프로그램은 대부분 Multi Thread 프로그램이다.

### 쓰레드의 구조

- 프로세스의 메모리 공간 공유 (code, data)
- 프로세스의 자원 공유 (file, i/o, ...)
- 비공유: 개별적인 PC, SP, registers, stack

> PC 는 공유하지 않는다. 서로 실행하는 위치를 같게하면 문제가 생기기 때문이다.

## References

http://www.kocw.net/home/search/kemView.do?kemId=978503

https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/4_Threads.html
