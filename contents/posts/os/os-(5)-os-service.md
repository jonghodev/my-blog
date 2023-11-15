---
title: OS (5) 운영체제 서비스
date: 2020-11-20 09:00:00
update: 2020-11-20 09:00:00
tags:
  - os
description:
series: OS 강의
---

## Process management

프로세스(process) 란 메모리에서 실행 중인 프로그램을 말한다. (program in execution)

주요 기능

- 프로세스의 생성, 소멸 (creation, deletetion)
- 프로세스 활동 일시 정지, 활동 재개 (suspend, resume)
- 프로세스간 통신 (interprocess communication: IPC)
- 프로세스간 동기화 (synchronization)
- 교착상태 처리 (deadlock handling)

## Main Memory management

주요기능

- 프로세스에게 메모리 공간 할당 (allocation)
- 메모리의 어느 부분이 어느 프로세스에게 할당되었는가 추적 및 감시
- 프로세스 종료 시 메모리 회수 (deallocation)
- 메모리의 효과적 사용
- 가상 메모리: 물리적 실제 메모리 보다 큰 용량 갖도록

## File management

Track/sector 로 구성된 디스크를 파일이라는 논리적 관점으로 보게한다. (추상화)

주요기능

- 파일의 생성과 삭제 (file creation & deletion)
- 디렉토리 (directory) 의 생성과 삭제
- 기본 동작 지원: open, close, read, write, create, delete
- Track/sector - file 간의 매핑 (mapping)
- 백업 (backup)

## Secondary storage management

하드 디스크, 플래시 메모리 등

주요 기능

- 빈 공관 관리 (free space management)
- 저장 공간 할당 (stroage allocation)
- 디스크 스케쥴링 (disk scheduling)

## I/O device management

주요기능

- 장치 드라이브 (Device drivers)
- 입출력 장치의 성능 향상: buffering, cahecing, spooling

## System call

운영체제 서비스를 받기 위한 호출이다.

Application Layer 의 Process 가 OS 의 Management 부서에 요청.

주요 시스템 콜

- **Process**: end, abort, load, execute, create, terminate, get/set attributes, wait event, signal event
- **Memory**: allocate, free
- **File**: create, delete, open, close, read, write, get/set arttributes
- **Device**: request, release, read, write

## References

http://www.kocw.net/home/search/kemView.do?kemId=978503
