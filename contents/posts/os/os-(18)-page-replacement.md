---
title: OS (18) Page Replacement
date: 2020-12-06 00:20:00
update: 2020-12-06 00:20:00
tags:
  - os
description:
series: OS 강의
---

## 페이지 교체

메모리 가득 차면 추가로 페이지를 가져오기 위해 어떤 페이지는 **Backing Store** 로 몰아내고 **(page-out)** 그 빈 공간으로 페이지를 가져온다. **(page-in)**

이때 **page-out** 된 페이지를 **Victim Page** 라고한다.

## Victim Page

Page out 된 페이지를 Victim Page 라고 한다.

어떤 페이지를 그러면 Victim Page 로 선택해야할지 생각해보자.

만약 Page out 이 된다면 그 이후에 몰아내진 Victim Page 는 Backing Store, 즉 하드 디스크에 다시 그 페이지를 기록해야할 것이다.

근대 만약 그 페이지가 변경된 적이 없는 페이지라면 다시 Backing Store 에 Write 를 할 필요가 없다. 이 Write 하는 것은 IO 시간을 유발하므로 가능한 피하는 것이 좋다.

Page Table 에는 modified bit (=dirty bit) 라는 것을 두어서 수정된 적이 없는 페이지를 가능한 골라서 그것을 몰아낸다. 이렇게하면 다시 Hardware 에 Write 를 하지 않아도 되기 때문이다.

## Page Repalcement Algorithm

- FIFO
- OPT
- LRU

### FIFO (First-In First-Out)

메모리에 올라온지 가장 오래된 페이지를 교체한다.

> Idea: 초기화 코드는 더 이상 사용이 안 될 것.

장점. 가장 간단한 페이지 교체 알고리즘이다.

단점. Belady's Anomaly

- 원래는 Frame 수가 증가하면 Page Fault 가 감소해야하는데
- FIFO 를 사용하면, Frame 수가 증가하면 Page Fault 가 증가할 때가 있다.

### OPT (Optimal)

앞으로 가장 오랜 시간동안 사용되지 않을 페이지를 교체한다.

장점. 가장 좋은 성능을 보인다.

단점. 비현실적이다. 앞으로 어떤 페이지가 사용되고 사용 안될지 미래를 예측할 수 없기 때문이다.

### LRU (Least-Recently-Used)

최근에 가장 늦게 사용된 페이지를 교체한다.

> Idea: 최근에 사용되지 않으면 나중에도 사용되지 않을 것.

OPT 는 미래를 보고 페이지를 선택하지만, LRU 는 과거를 보고 페이지를 선택한다.

과거를 보면, 최근에 사용 안된 페이지는 앞으로도 안 될 확률이 높다는 확률을 기반으로 한다.

# Global vs Local Replacement

Global replacement: 메모리 상의 모든 프로세스 페이지에 대해 교체

Local replacement: 메모리 상의 자기 프로세스 페이지에 대해 교체

성능 비교: Global replacement 가 더 효율적일 수 있다.

## References

http://www.kocw.net/home/search/kemView.do?kemId=978503

http://contents.kocw.or.kr/KOCW/document/2013/kyungsung/yangheejae/os05.pdf

https://faithpac27.tistory.com/entry/%EC%93%B0%EB%A0%88%EC%8B%B1-Thrashing-%EC%9D%B4%EB%9E%80
