---
title: OS (13) Deadlocks
date: 2020-12-01 10:50:00
update: 2020-12-01 10:50:00
tags:
  - os
description:
series: OS 강의
---

정리를 한 번 하고 시작하자.

OS 는 Hardware 를 관리하는 일을 한다. 따라서 Resource Manager 라고도 불린다.

Computer Resource 에서 가장 중요한 Resource(자원)는 뭘까?

그것은 CPU 다.

OS 안에 Process Management 부서에선 크게 다음과 같은 일을 한다.

- CPU Scheduling
- Process Synchronization
  - Deadlocks

Process Synchronization 에 관한건 [이곳](</os/os-(12)-process-synchronization/>)에서 알아봤고

이번엔 좀 더 구체적으로 Deadlocks 에 관해 알아보자.

## 데드락(Deadlocks) 이란

프로세스가 자원을 얻지 못해서 다음 처리를 처리하지 못하는 상태이며, **교착 상태**라고도 부른다.

시스템의 한정적인 자원을 여러 곳에서 사용하려할 때 생기는 문제다.

## 교착상태의 필요조건

- Mutual exclusion (상호대기)
- Hold and Wait (보유 및 대기)
- No Preemption (비선점)
- Circular wait (환형대기)

충분조건이 아닌 필요조건이기 때문에 저것들이 모두 발생한다고 하더라도 꼭 **교착상태(deadlock)**이 발생하는 것은 아니다.

## 교착상태 처리

1. 교착상태 **방지**: Deadlock Prevention
2. 교착상태 **회피**: Deadlock Avoidance
3. 교착상태 **검출 및 복구**: Deadlock Detection & Recovery
4. 교착상태 **무시**: Dont'care

## 교착상태 처리: Deadlock Prevention

교착상태의 필요조건인 다음 네 가지를 불만족 시키게 한다.

- Mutual exclusion (상호대기)
- Hold and Wait (보유 및 대기)
- No Preemption (비선점)
- Circular wait (환형대기)

### 상호배타 (Mutual exclusion)

자원을 공유가능하게 한다. 하지만 원천적으로 불가능할 수 있다.

### 보유 및 대기 (Hold & Wait)

자원을 가지고 있으면서 다른 자원을 기다리지 않게 한다.

예를들어 식사하는 철학자 문제를 생각해보자.

모든 철학자가 동시에 배고파서 오른쪽에 있는 젓가락을 들고 왼쪽을 보면 젓가락이 없을 것이다. 이때 젓가락이 생길때까지 기다리는 것이 아니라

자신이 든 젓가락을 내려 놓아서 자원을 포기하는 것이다.

해당 문제의 단점으로는 자원 활용률이 저하되어 기아 현상이 발생할 수 있다.

컴퓨터 입장에서보면 어떤 프로세스가 자원을 사용하려고 점유하고 그 다음 자원을 점유하려 했는데 이미 다른 프로세스가 사용중이란 이유로 자신이 처음에 점유한 자원을 내려놓는 것은 자원 활용률이 떨어진다고 볼 수 있다.

### 비선점 (No preemption)

자원을 선점 가능하게한다. 하지만 이것또한 원천적으로 불가능할 수 있다.

예를들어 프린터의 경우, 어떤 사람이 프린터를 사용해 출력하고 있는데 이것을 선점해서 다른 프로세스가 가로챈다면 일어나선 안 될 상황이 발생하게 될 것이다.

### 환형대기 (Circular wait)

자원에 번호를 부여한다. 그리고 번호를 오름차순으로 자원을 요청한다.

환형대기가 일어났을 때, 자원 할당도를 통해 보면 원이 생긴 것을 볼 수 있다. 이때 하나의 흐름만 끊으면 환형 대기는 일어나지 않게 된다.

단점으로는 자원 활용률이 저하될 수 있다.

## 교착상태 회피: Deadlock Avoidance

Banker's Algorithm 에 기인한 해결방법으로, Safe allocation 과 Unsafe allocation 을 고려하는 것이다.

프로세스가 자원에 대한 요청을 할 때, 미리 나중에 교착상태가 발생할지를 검사하고 할당을 해줌으로써 회피를 할 수 있다.

## 교착상태 검출 및 복구: Deadlock Detection & Recovery

우선 교착상태가 일어나는 것을 허용하고 주기적으로 검사를 한다. 그리고 발견이 되면 복구 시키는 방법이다.

검출 방법으로는 자원이 요청될 때 혹은 주기적으로 교착상태가 일어났는지를 검사할 수 있다. 하지만 단점으로, 오버헤드가 있는 방법이다.

복구 방법으로는 프로세스를 일부 강제 종료시키거나, 자원을 선점하여 다른 프로세스에게 할당하는 방법이 있다.

## 교착상태 무시: Don't care

위에서 언급한 4가지 필요조건을 모두 만족한다고 해도 꼭 일어나는 것은 아니기 때문에, 교착상태는 실제로 잘 일어나지 않는다고 한다.

OS Level 에서 교착상태 발생시 사용자는 컴퓨터를 재시동하는 방법을 사용해 해결한다.

교착상태가 잘 일어나지 않는다는 의미는, 보통 Windows 나 MacOS 와 같이 개인용 컴퓨터로, 일반적으로 한 명의 사용자가 사용하는 컴퓨터는 하나의 자원에 여러 개의 프로세스가 접근할 일이 많이 없어서 교착 상태가 잘 일어나지 않는다는 것 같다.

하지만 실무 레벨에서 개발을 하다보면 자주 발생하는 문제다.

## References

http://www.kocw.net/home/search/kemView.do?kemId=978503

http://contents.kocw.or.kr/KOCW/document/2013/kyungsung/yangheejae/os03.pdf
