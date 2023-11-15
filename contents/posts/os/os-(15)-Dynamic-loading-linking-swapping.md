---
title: OS (15) Dynamic Loading/Linking, Swappping
date: 2020-12-03 13:00:00
update: 2020-12-03 13:00:00
tags:
  - os
description:
series: OS 강의
---

## 동적 적재 (Dynamic Loading)

동적 적재라는 프로그램에 실행헤 반드시 필요한 루틴/데이터만 적재하는 것을 말한다.

우리가 작성한 소스코드의 코드 루틴과 데이터는 항상 모든 상황에서 사용되는 것이 아니다. 이것을 전부다 메모리에 올리는 것은 효과적이지 않기 때문에 사용할 때만 메모리에 적재하는 것이 동적 적재다. 반대로 정적 적재 (Static loading)라는 것도 있다.

## 동적 연결 (Dynamic Linking)

동적 연결이란 여러 프로그램에 공통적으로 사용되는 라이브러리를 동적으로 연결하는 것을 말한다.

여러 프로세스가 공통 라이브러리를 사용하는 경우는 많을 것이다. 예를들어 `print()` 라는 라이브러리 루틴에 있는 함수는 여러 프로세스가 사용할 것이다.

그런데 이것을 각각의 프로그램마다 실행파일로 만들게 되면 실행파일의 용량도 커질뿐더러, 중복되는 코드를 메모리에 많이 올리게 된다.

과거에는 정적 링킹으로, 프로그램을 만들 때 linking 을 해서 라이브러리 루틴을 미리 연결했다. 하지만 동적 연결에서는 함수를 복사하지 않고 함수의 위치정보만 갖게해서 그 함수를 호출할 수 있게 만든다.

현대 시스템은 동적 연결을 사용해서 실행 시까지 링킹을 미룬다.

이렇게 함으로써 오직 하나의 라이브러리 루틴만 메모리에 적재되게 된다.

Linux 에서는 공유 라이브러리 (share library) 라고 부르며, Windows 에서는 동적 연결 라이브러리(Dynamic Linking Library) 라고 부른다.

## 스와핑 (Swapping)

스와핑이란, 메모리에 적재되어 있으나 현재 사용되지 않고 있는 프로세스를 **Backing Store (=swap device)** 로 몰아내는 것을 말한다.

**Swap out** 을 통해 **Backing store** 로 밀어내며 **Swap in** 을 통해 다시 메모리에 적재한다.

MMU 의 Relocation Register 덕분에 적재 위치는 신경쓰지 않을 수 있다.

단점으로, 프로세스의 크기가 크면 Backing Store 입출력에 따른 부담이 크다.

## References

http://www.kocw.net/home/search/kemView.do?kemId=978503

http://contents.kocw.or.kr/KOCW/document/2013/kyungsung/yangheejae/os04.pdf
