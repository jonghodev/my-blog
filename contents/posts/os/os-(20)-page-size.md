---
title: OS (20) Page Size
date: 2020-12-08 07:00:00
update: 2020-12-08 07:00:00
tags:
 - os
description:
---

일반적 크기: 4KB -> 4MB

현대에는 프로세스와 메모리 용량도 커지면서 페이지 사이즈도 **점차 커지는 경향**이다.

### 페이지 크기 영향

- 내부 단편화: 페이지 크기가 작을수록 내부 단편화가 적어진다.
- Page-in, page-out 시간: page fault time 은 seek time 이 많이 차지하는데, page size 가 클수록 한 번에 들고오는 페이지가 크므로 유리하다.
- 페이지 테이블 크기: Page Table 은 SRAM 으로 만들기 때문에 Entry 가 적을수록 유리하다. 그러므로 페이지 사이즈가 클수록 Page Table Entry 가 작아지므로 유리하다. (비용 감소)
- Page fault 발생 확률: 한 번에 많이 들고오므로 페이지 사이즈가 클수록 좋다.
- Memory Resolution: 페이지가 작을수록 정밀하므로 유리하다.

> Memory Resolutin: 정밀도를 의미한다. 우리가 실제로 필요로하는 내용만 메모리에 있는 것을 말한다.

## TLB

옛날에는 캐시 메모리를 별도에 칩에 두었다. 현대는 반도체 집적 기술이 좋아서 캐시 메모리를 CPU 안에 넣어둔다.

TLB 역시 on-chip 내장이다. (CPU 내장)

## References

http://www.kocw.net/home/search/kemView.do?kemId=978503

http://contents.kocw.or.kr/KOCW/document/2013/kyungsung/yangheejae/os05.pdf
