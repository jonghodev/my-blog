---
title: OS (3) Modern OS
date: 2020-11-18 07:00:00
update: 2020-11-18 07:00:00
tags:
 - os
description:
---

## 다중 프로세서 시스템 (Multiprocessor system)

### 병렬 시스템 (parallel system)

CPU 가 여러 개가 된다.

### 강결합 시스템 (tightly-coupled system)

여러개의 CPU 가 하나의 Memory 에 결합된다.

### 3가지 장점

1. Performance: CPU 가 여러 개 일수록 당연히 유리하다.
2. Cost: 일반적으로 하나의 고성능 CPU 를 두는 것보다 낮은 CPU 를 여러 개 두는 것이 더 유리하다.
3. Reliability: 하나의 CPU 가 고장나도 다른 CPU 는 동작하게 된다.

CPU 가 여러 개가 되기 때문에 스케쥴링이 다르게 된다.

→ 이부분은 이 강의에서 다루지 않고, 대학원 같은 곳에서 다룬다고 한다.

## 분산 시스템 (Distributed system)

- 다중 컴퓨터 시스템 (multi-computer system)
- 소결합 시스템 (loosely-coupled system)
- **분산 운영체제 (Distributed OS)**

## 실시간 시스템 (Real-time system)

- 시간 제약: Deadline
- 공장 자동화 (FA), 군사, 항공, 우주
- **실시간 운영체제 (Real-time OS = RTOS)**

## 인터럽트 기반 시스템

현대 운영체제는 인터럽트 기반 시스템이다.

### ROM 부팅이 끝나면

- 운영체제는 메모리에 상주 (resident)
- 사건 (event) 을 기다리며 대기: 키보드, 마우스

### 하드웨어 인터럽트 (Hardware interrupt)

- 인터럽트 결과 운영체제 내의 특정 코드 실행 (ISR)
- Interrupt Service Routine 종료 후 다시 대기

### 소프트웨어 인터럽트 (Software interrupt)

- 사용자 프로그램이 실행되면서 소프트웨어 인터럽트 (운영체제 서비스 이용을 위해)
- 인터럽트 결과 운영체제 내의 특정 코드 실행 (ISR)
- ISR 종료 후 다시 사용자 프로그램으로

## References

http://www.kocw.net/home/search/kemView.do?kemId=978503
