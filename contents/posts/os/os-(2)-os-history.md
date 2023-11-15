---
title: OS (2) OS History
date: 2020-11-17 10:11:82
update: 2020-11-17 10:11:82
tags:
  - os
description:
series: OS 강의
---

## Batch processing system

Memory 에는 OS 와 User Program 1개가 올라간다.

IO 시간 동안 CPU Idle time 을 갖게 된다.

## Multi programming system

이전에는 Memory 에 하나의 프로그램만 올라갔지만

이젠 Memory 에 여러 개의 User Program 을 갖게 된다.

CPU 는 상대적으로 비싼 자원이다. 그런데 빠른 CPU 연산 속도에 비해 IO 는 느리다.

CPU 의 Idle time 을 줄이기 위해 IO 같은 요청이 들어왔을 때 다른 작업을 처리하게 한다.

### CPU Scheduling

어느 순서로 실행해야 속도가 빠를까?

### Memory 관리

어디에 두어야 공간을 효율적으로 사용했을까?

User Program 이 많아졌기 때문에 Memory 를 관리해야 한다.

### 보호

다른 영역의 프로그램을 침범하지 못하게 해야 한다.

## Time-sharing system (TSS)

Unix 가 대표적인 TSS 시스템이다. 우리가 현대에 사용하는 대부분의 시스템도 TSS 다.

거의 여기서 OS가 완성됐다.

### Interactive

모니터, 키보드의 등장으로 컴퓨터와 상호작용(Interactive)이 가능해진다.

### Time-sharing

옛날에는 컴퓨터는 비쌌다. 그래서 하나의 컴퓨터에 여러개의 단말(Terminal) 이 접속해서 여러 명의 사용자가 하나의 컴퓨터를 사용했다.

그런데 기존의 방식으로는 여러 사용자가 메모리에 올린 프로그램을 처리할 수 없었다. 왜냐면 CPU 가 하나이기 때문에 하나의 Program 만 처리할 수 있기 때문이다.

그래서 시분할을 통해 1ms, 10ms 정도의 속도로 Switching 하며 여러 개의 프로그램을 처리하게 한다.

그리고 일정 시간이 지나면 강제 절환이 된다.

CPU는 매우 빠르게 Switching 을 하기 때문에 사용자 입장에서는 자신이 컴퓨터를 독점한다고 느끼게 된다.

### 프로세스간 통신

여러 사용자 간에 통신을 위해 프로세스간 통신이 일어난다.

### 동기화 (Synchronization)

### 가상 메모리 (Virtual Memory)

하드메모리 디스크를 메인메모리처럼 사용하게 한다.

## References

http://www.kocw.net/home/search/kemView.do?kemId=978503
