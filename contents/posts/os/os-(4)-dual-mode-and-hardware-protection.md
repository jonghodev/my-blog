---
title: OS (4) Dual mode and Hardware protection
date: 2020-11-19 07:00:00
update: 2020-11-19 07:00:00
tags:
 - os
description:
---

## 이중모드

OS 의 이중모드에는 User mode, System mode 가 있다.

권한을 필요로 하는 **Privileged Instruction** 은 System mode 에서만 접근할 수 있다.

User mode 와 System mode 를 내부적으로 어떻게 저장할까?

CPU 에는 레지스터에 여러 비트들이 기록된다. 그리고 Monitor 라는 레지스터가 있는데 이 레지스터의 값이 1이면 System mode, 0 이면 User mode 인것으로 동작한다.

## 하드웨어 보호

### 입출력장치 보호

입출력 명령은 Privileged Instruction 이다. 그래서 입출력을 하려면 OS에게 요청해야 한다.

입출력 명령을 위해 OS 에 요청을 하면 system mode 로 전환이 된다. OS 는 입출력을 대행하고, 요청을 마친 후 다시 user mode 로 복귀한다.

올바른 요청이 아니면 OS 는 거부하고 (Privileged instruction violation) ISR 이 동작해 해당 프로그램을 강제 종료시킨다.

### 메모리 보호

**Memory Management Unit**(MMU)

OS 가 설정한 Base 와 Limit 사이의 메모리 참조만 허락한다.

만약 허락되지 않는 주소에 접근하려는 경우 MMU 에서 CPU 로 (Segment violation) ISR 을 걸어서 해당 프로그램을 강제 종료시킨다.

### CPU 보호

한 사용자가 실수 또는 고의로 CPU 시간을 독점

- 예: while (n=1)
- 다른 사용자의 프로그램 실행 불가

해결법

- Timer 를 두어 일정 시간 경과 시 타이머 인터럽트 (RR: Round Robin)
- 인터럽트 > 운영체제 > 다른 프로그램으로 강제 전환

## References

http://www.kocw.net/home/search/kemView.do?kemId=978503
