---
title: OS (retrospect) fundamentals end
date: 2020-12-06 22:12:12
update: 2020-12-06 22:12:12
tags:
  - os
description:
series: OS 강의
---

[여기서](</os/os-(0)-OS-가-왜-중요할까/>)부터 시작해서 **3주**란 시간 동안 경성대학교 양희재 교수님이 수업하시는 [강의](http://www.kocw.net/home/search/kemView.do?kemId=978503)를 통해 기본 OS 를 공부했다.

3주라는 짧다면 짧고 길다면 긴 시간이었지만 동안 OS 에 관해서 공부를 하며 느낀 점이 있다.

내가 OS 의 기초적인 부분만 공부하긴 했지만, 내용이 쉬웠고 많은 것을 알게된 것 같다. low input high return.

옛날에 두루뭉실하게 알았던 내용들.

- Thread 와 Process 의 관계
- Context Switching Overhead
- race condition, deadlock

이러한 내용을 배우면서 좀 더 확실하게 내가 실무에서 사용하는 기술들이 어떤 원리로 동작하는지 이해할 수 있게 됐다.

OS 를 공부함으로써 내가 실무에서 사용하는 기술, Java, Node.js, React Native, AWS 같은 기술들을 더 잘 알게된건 아니다.

하지만 근본적으로 프로세스와 메모리가 어떻게 관리되고, 위 기술들이 어떻게 OS 와 상호작용하는지 알 수 있는 기회가 되었고

실무에서 접하게 되는 동시성 문제가 OS 에서는 어디서 발생했고, 이를 어떻게 해결했는지도 알아가는 재미가 아주 좋았다.

나중에 좀 더 Kernel 에 대해서 깊게 공부하고 싶다.

[블로그](https://covenant.tistory.com/100)에서 추천해주는 강의인 고건 교수님 Kernal of Linux 강의를 보아서 Kernel 을 깊게 공부해고

Linux 같은 OS 소스코드를 분석하거나 책을 보면서 OS 를 간단하게라도 구현해보는 일을 해볼 것이다.

그리고 강의에서 컴퓨터 구조 수업을 들은 학생들을 대상으로 수업하다보니 컴퓨터 구조 수업시간에 알려준 버스 이야기나 캐시 메모리에 대한 이야기가 몇 번 언급이 되었는데 어느정도는 이해가 가니 수업 진행에는 전혀 문제가 없었으나 더 꼼꼼히 다 알고싶은 마음이 들어서. 컴퓨터 구조에 대한 책도 봐야겠다는 생각이 들었다.

그리고 강의를 볼 정도로 아예 개념 지식이 없는게 아니라 기본적인 CPU, Memory 구조 같은 것은 알고 있으니 책을 통해서 부족한 부분을 채울 예정이다.

내가 강의를 보며 OS 에 대해 짧게나마 정리한 글들 목차이다.

[OS (0) Why OS Important?](</os/os-(0)-why-os-important/>)

[OS (1) OS Introduction](</os/os-(1)-os-introduction/>)

[OS (2) OS History](</os/os-(2)-os-history>)

[OS (3) Modern OS](</os/os-(3)-modern-os/>)

[OS (4) Dual mode and Hardware protection](</os/os-(4)-dual-mode-and-hardware-protection/>)

[OS (5) OS Service](</os/os-(5)-os-service/>)

[OS (6) Process](</os/os-(6)-process/>)

[OS (7) Process Queue](</os/os-(7)-process-queue/>)

[OS (8) Multiprogramming](</os/os-(8)-multiprogramming/>)

[OS (9) CPU Scheduling](</os/os-(9)-cpu-scheduling/>)

[OS (10) Process creation and termination](</os/os-(10)-process-creation-and-termination/>)

[OS (11) Thread](</os/os-(11)-thread/>)

[OS (12) Process Synchronization](</os/os-(12)-process-synchronization/>)

[OS (13) Deadlocks](</os/os-(13)-deadlocks/>)

[OS (14) Program and memory](</os/os-(14)-program-and-memory/>)

[OS (15) Dynamic Loading/Linking, Swapping](</os/os-(15)-dynamic-loading-linking-swapping/>)

[OS (16) Memory Allocation](</os/os-(16)-memory-allocation/>)

[OS (17) Virtual Memory](</os/os-(17)-virtual-memory/>)

[OS (18) Page Replacement](</os/os-(18)-page-replacement/>)

[OS (19) Allocation of Frames](</os/os-(19)-allocation-of-frames/>)

[OS (20) Page Size](</os/os-(20)-page-size/>)

[OS (21) File Allocation](</os/os-(21)-file-allocation/>)

[OS (22) Disk Scheduling](</os/os-(22)-disk-scheduling/>)
