---
title: OS (8) Multiprogramming
date: 2020-11-23 10:30:00
update: 2020-11-23 10:30:00
tags:
 - os
description:
---

메인 메모리에 여러 프로그램을 올리는 것을 말한다.

- Degree of multiprogramming
- i/o-bound vs CPU-bound process
- Medium-term scheduler
  - Swapping
- Context switching
  - Scheduler
  - Dispatcher
  - Context switching overhead

## Degree of multiprogramming

메모리에 몇 개의 프로세스가 올라와있는지를 말한다.

## i/o-bound vs CPU-bound process

Job scheduler 는 io bound, cpu bound program 중 어떤 것을 올릴지도 결정한다.

## Medium-term scheduler

Medium-term scheduler 란 스와핑에 관한 것이다. 메모리에 있는 프로세스를 줄여 degree of programming 을 낮춘다.

메인 메모리에서 일정 시간 동안 활동하지 않는 프로세스를 Hard disk 의 Backing store 에 swap out 시킨다.

그리고 다시 사용될 때 아무 주소에 Swap in 된다. 이것을 Virtual Memory 라고도 한다.

Backing store 에 있는 Process 중 어떤 Process 를 Memory 에 올릴지도 결정힌다.

> Hard disk 는 File system 과 Backing store (Swap device) 로 나뉜다.

## Context switching

switching 이 일어날 때 현재 상태, register 값이나 MMU(base, limit) 값 같은 것을 PCB 에 저장한다.

다시 전환될 때 해당 Process 의 PCB 에서 Restore 해서 기억된 위치로 이동한다.

OS 안의 Process Management 의 Dispatcher 가 이러한 Context 를 저장하고 Restore 하는 일을 한다.

또한 이러한 작업에는 Overhead (Context Switching Overhead) 가 있다. 그래서 간단한 Algorithm 이 사용된다. 그래서 Overhead 를 줄이기 위해 C 같은 High Level Language 가 아니라 Assembly 로 프로그래밍된다.

## References

http://www.kocw.net/home/search/kemView.do?kemId=978503
