---
title: OS (9) CPU scheduling
date: 2020-11-24 07:00:00
update: 2020-11-24 07:00:00
tags:
  - os
description:
series: OS 강의
---

CPU Scheduling 은 Ready Queue 에서 어떤 Job 이 CPU 의 실행이 될지 정한다.

- Preeemptive vs Non-preemptive
- Scheduling criteria
  - CPU Utilization (CPU 이용률)
  - Throughput (처리율)
  - Turnaround time (반환시간)
  - Waiting time (대기시간)
  - Response time (응답시간)

## CPU Scheduling Algorithms

- First-Come, First Served (FCFS)
- Shortest-Job-First (SJF)
  - Shortest-Remaining-Time-First
- Priority
- Round-Robin (RR)
- Multilevel Queue
- Multilevel Feedback Queue

### First-Come, First Saved (FCFS)

가장 간단하고 공평한 방법이지만, 꼭 좋은 성능을 나타내는 것은 아니다.

은행, 병원 같은 곳에서 이용되는 방식이다.

FIFO 큐를 사용하여 간단하게 구현할 수 있다.

호위 효과: 버스트 시간이 긴 하나의 프로세스가 CPU 를 양도할 때까지 다른 모든 프로세스들이 기다리는 현상이다.

### Shortest-Job-First (SJF)

새로운 프로세스가 도착할 때마다 새로운 스케줄링이 이루어진다.

하지만, 다음 CPU 버스트 시간을 측정하는 것이 매우 어렵다는 단점이 있다.

### Priority Scheduling

우선순위를 통해 스케쥴링 순서를 정한다.

우선순위를 정하는 요소는, 내부 요소와 외부 요소가 있다.

starvation: 아무리 기다려도 우선순위에 의해 자기 차례가 오지않을 수 있다.

aging: 따라서 ready queue 에 있을수록 우선순위를 올려주도록 한다.

> 보통 숫자가 낮울수록 우선순위가 높다.

### Round-Robin

일정시간이 지나면 넘어간다.

좋은 퀀텀을 사용하는 것이 성능에 직결되므로 중요하고 그것에 의존적이다.

Time quantam 이 무한대라면 하나의 프로세스가 끝날때까지 진행되므로 FCFS 와 같다.

퀀텀이 작아질수록 응답 시간이 줄어들어 사용자에게 빠른 응답을 제공할 수 있지만, Context Switching Overhead 가 높아지므로 효율이 떨어진다. 따라서 적정한 퀀텀을 사용해야 하며, 보통 10~100 mesc 를 사용한다.

### Multilevel Queue Scheduling

Queue 를 하나를 둘 필요 없다. Process 를 그룹으로 나누어서 그룹마다 Queue 를 둔다.

예) 은행 대출 줄, 입출금 줄

→ System process queue, Interactive process queue, ...

![](./images/2020-11-24-multilevel-queue-scheduling-3.png)

System process 에는 Priority scheduling

Interactive Process 에는 Round Robin 에 퀀텀을 4 msec 로.

Interactive editing process Round Robin 에 퀀텀을 10 mesc 로 주는 둥 각기 큐마다 다른 스케쥴링 알고리즘을 적용할 수 있다.

### Multilevel Feedback Queue

기존의 다단계 큐 스케줄링에서는 프로세스의 특성이 바뀌지 않는다고 보아 프로세스가 큐 사이를 이동하는 것을 허용하지 않았다. 스케줄링 오버헤드는 적다는 장점이 있지만 융통성이 부족하다는 단점이 있다.

이 문제를 극복하기 위해 나온 것이 다단계 피드백 큐 스케줄링이다. 프로세스가 큐 사이를 이동하는 것이 허용된다. 큐를 구분하는 기준은 CPU 버스트이다. 이 방법에서는 입출력 중심의 프로세스와 대화형 프로세스를 높은 우선순위의 큐에 넣는다. 반대로 낮은 우선순위의 큐에서 너무 오래 대기하는 프로세스들은 높은 곳으로 이동(노화)하여 기아 상태를 예방한다. 다음의 큐 3개를 가정하자.

- 큐 0: RR, quantum 8ms
- 큐 1: RR, quantum 16ms
- 큐 2: FCFS

CPU 버스트가 8ms 이하인 프로세스는 최고의 우선순위로 실행될 것이다. 8ms~24ms 인 프로세스는 그 다음의 우선순위를 받게 될 것이다. 그리고 CPU 버스트가 너무 긴 프로세스는 큐 2로 가게 될 것이다.

다단계 큐와 비교하였을 때 단점은, 프로세스를 다른 큐로 올려주거나 내리는 시기를 결정하는 등 큐 간 이동가능성 때문에 고려해야할 것이 많아 설계하기 복잡하다는 것이다.

### 현대 운영체제

현대 운영체제는 위에서 언급한 알고리즘을 모두 사용한다. 다양한 큐를 두고 다양한 정책을 두어 사용한다.

## References

http://www.kocw.net/home/search/kemView.do?kemId=978503

https://oaksong.github.io/2018/02/12/cpu-scheduling/
