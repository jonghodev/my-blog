---
title: OS (16) Memory Allocation
date: 2020-12-04 07:00:00
update: 2020-12-04 07:00:00
tags:
 - os
description:
---

메모리 할당에 대해서 알아보자. 과거의 방법부터 현대의 방법까지 알아보자.

## 연속 메모리 할당 (Contiguous Memory Allocation)

다중 프로그래밍 환경

- 부팅 직후 메모리 상태: OS + Big single hole
- 프로세스 생성 & 종료 반복 -> Saccttered holes

현대 다중프로그래밍 환경에서 컴퓨터가 부팅이되면 OS 가 메모리에 올라오고 남은 공간은 텅 비어있다. (Big single hole)

그리고 프로세스가 생성되면 메모리가 할당이 되어가며 점점 메모리에 프로세스가 들어설 것이다. 그리고 프로세스가 종료되면 그 자리가 비게 되는데

프로세스는 자신의 크기에 맞는 메모리 번지에 들어가려 한다. 그런데 프로세스 생성과 종료가 반복되다 보면 메모리 크기가 작은 번지수들이 많아지고

프로세스들의 크기가 상대적으로 커서 그곳에 들어갈 수 없어서 그 메모리를 활용하지 못하게 된다. 이것을 메모리 외부 단편화(Memory External Fragmentation)라고 한다.

### 연속 메모리 할당 방식

- First-fit (최초 적합)
- Best-fit (최적 적합)
- Worst-fit (최악 적합)

연속 메모리 할당 방식에 따른 메모리 외부 단편화를 최적화하기 위한 방식 메모리 할당 방식으로 위의 방식이 있다.

**최초 적합**은 프로세스가 들어갈 자리를 고민하지 않고 바로 보이는 곳에 들어가는 것이다.

**최적 적합**은 프로세스가 메모리를 탐색한 후 자기의 크기와 가장 비슷한 공간에 들어가는 것이다.

**최악 적합**은 프로세스가 자신의 크기보다 가장 큰 메모리 공간에 들어가는 것이다.

예를들어 다음과 같은 Hole 과 Process 들이 있다고 하자.

Hole: 100 500 300 200 400 KB
Process: 150 200 250 350 KB

**최초 적합**의 경우, 150 KB Process 는 가장 가까이 보이는 500 KB 공간에 들어갈 것이고, 200 KB 프로세스는 300 KB 공간에 들어갈 것이다. 이후는 생략한다.

**최적 적합**의 경우, 150 KB Process 는 자신의 크기와 가장 가까운 200KB 공간에 들어갈 것이고 200KB Process 는 200 KB 공간에 들어가고, 250KB Process 는 300 KB 공간에 들어갈 것이고 이후는 생략한다.

**최악 적합**의 경우, 150 KB Process 는 500 KB 공간에 들어가고, 200 KB Process 는 400 KB 공간에 들어가고 250 KB Process 는 이전에 150 KB 프로세스가 들어가고 생긴 350KB 이상의 공간에 들어갈 것이다.

이처럼 서로 다른 메모리 할당 방식이 있다. 이것들에 대한 성능을 비교해보면, 메모리 공간을 탐색하지 않고 바로 들어가기만 하면 되는 First Fit 이 속도가 가장 빠를 것이다.

그리고 이용률은 Best Fit 이 가장 좋고, 연구 결과 First Fit 과 거의 차이가 없다고 한다. Worst Fit 은 항상 안 좋다고 한다.

이러한 방법을 사용해도 결국 외부 단편화로 인한 메모리 낭비는 **1/3** 수준이라고 한다. 이것은 너무 많은 손해를 초래하게 된다.

### Compaction

외부 단편화를 방지하기 위한 방법으로, **Compaction** 이라는 방법이 있다. 이것은 메모리 단편화로 나뉜 공간들을 모아주는 것으로 프로세스들의 메모리 번지수를 옮겨서 메모리 단편화된 공간을 모아준다.

그런데 이 방법을 사용하면 기존 프로세스들에게 영향이 많이 가므로 최대한 프로세스들의 이동을 적게하는 알고리즘을 사용해야 하는데

현재까지 일반적으로 최적의 알고리즘이 없고 오버헤드가 너무 큰 방법이라고 할 수 있다.

## 페이징 (Paging)

위의 방식들은 연속 메모리 할당이라는 컨텍스트 안에서 고안해낸 방식들이다. **페이징**은 연속 메모리 할당이라는 틀을 깨부신 방법으로, 메모리를 연속적으로 할당하지 않는 방식이다.

즉, 프로세스를 일정 크기(=페이지)로 잘라서 일정 크기로 잘린 메모리(=프레임)에 넣는 것이다. 이렇게 하면 단편화가 줄어든다.

> 프로세스는 페이지(Page)로 나뉘고, 메모리는 프레임(Frame)으로 나뉜다.

프로세스가 잘리기 때문에 그것을 실행하는 CPU 입장에서는 말이 안 될수도 있지만 MMU, Page Table 을 활용해서 그 문제를 해결한다.

이전에 Logical Address 를 Pysical Address 에 Mapping 할 때 MMU 는 CPU 로 하여금 가상의 주소를 보게하고 실제로는 실제 물리 메모리에 매핑을 해주었다.

여기서도 MMU 는 내부에 페이지 테이블을 두어서 그 잘린 페이지들을 프레임으로 연결해준다. 어떻게? MMU 내의 Relocation Register 의 값을 바꿔줌으로써.

## 주소 변환 (Address Translation)

![](./images/2020-12-04-translation.png)

### 논리주소

- CPU 가 내는 주소는 2 진수로 표현 (전체 m 비트)
- 하위 n 비트는 오프셋(offset) 또는 변위(displacement)
- 상위 m - n 비트는 페이지 번호

### 주소 변환: 논리주소 -> 물리주소

- 페이지 번호(p) 는 페이지 테이블 인덱스 값
- p 에 해당하는 테이블 내용이 프레임 번호(f)
- 변위(d) 는 변하지 않음

### 예제

- Page size= 4 bytes
- Page Table: 5 6 1 2
- 논리 주소 13 번지는 물리주소 몇 번지?

Page size 가 4 bytes 이므로, 2의 2승이어서 2비트로 쪼개야 한다. 뒤의 2비트는 displacement 가 된다.

논리주소 13 을 2진수로 변환하면 1101 이다. 앞의 뒤의 01은 displacement 로 offset 이므로 가만히 두고

앞에 있는 11을 10진수로 변환시키면 3이다. 이것을 Page Table 과 매핑시면 3번째 인덱스에 있는 Frame 은 2가 된다.

그 2(f)를 다시 이진수로 변환시켜서 displacement 와 합쳐주면 1001이 된다. 따라서 논리주소 13 번지는 물리주소 1001번지, 10진수로는 9번지가 된다.

## 내부 단편화

연속 메모리 할당으로 인한 메모리 외부 단편화를 페이징을 통해 해결했어도 여전히 문제는 남아 있다.

프로세스의 크기가 페이지 크기의 배수가 아니라면, 마지막 페이지는 한 프레임을 다 채울 수 없다. 그 남은 공간은 메모리 낭비가 된다.

예를들어 4byte 가 페이지 사이즈인 시스템에서 17 byte 프로세스가 필요한 페이지 개수는 5개로, 1개의 페이지는 3byte 가 남게 된다.

그래서 최대 메모리 낭비 값은 **페이지 크기 - 1** 이다.

외부 단편화에 비하면 내부 단편화는 매우 작은 값들이 생긴다.

## 페이지 테이블, TLB

MMU 안에 있는 페이지 테이블을 어디에 두어야할까? 바로 떠오르는 곳은 CPU 와 메모리다.

만약 CPU 에 둔다면 CPU 기억 장치에 두기 위해서 Register 에 두어야 할 것이다. 따라서 주소 접근할 때의 속도는 매우 빠르겠지만 한정된 CPU Register 의 크기 제한상

많은 테이블 엔트리를 둘 수 없다. 그렇다면 메인 메모리에 둔다면 상대적으로 GB 단위를 사용하는 메모리 입장에서 페이지 테이블의 엔트리 개수는 큰 문제가 안되지만

메모리에 접근할 때 속도가 느려서 성능 이슈가 있다는 단점이 있다.

따라서 [TLB](https://en.wikipedia.org/wiki/Translation_lookaside_buffer) (Translation Look-aside Buffer) 라는 Cache 를 둔다. TLB 는 SRAM 으로 되어있다.

> Main Memory 는 속도가 느린 DRAM 으로 만들어진다.

CPU 와 Memory 의 중간으로 보면 된다. CPU 보다는 느리지만 Table Entry 개수를 늘릴 수 있고, 메모리보다 Table Entry 개수는 적지만 속도는 빠르게 할 수 있다.

**TLB**는 모든 페이지 테이블을 저장할 수 없다. 공간이 한정적이기 때문이다. 하지만 TLB Hit Ratio 는 95% 이상이다.

따라서 CPU 가 메모리를 접근하게 되면 우선 MMU 의 TLB 에 접근을 해서 메모리 번지를 찾고(Hit Ratio, 보통 95% 이상) 만약 찾지 못했다면 (Miss) 메모리에 접근을 한다.

### Effective Memory Access Time

유효 메모리 접근 시간 (Effective Memory Access Time) 을 구해보자.

Memory 를 읽는데 걸리는 Tm, TLB 를 읽는데 걸리는 시간을 Tb, Hit Ratio 를 h 라고 하자.

그렇다면, **Effective Memory Access Time**= h \* (Tb + Tm) + (1 - h) \* (Tb + Tm) 이다.

## 보호와 공유

### 보호 (Protection)

CPU 가 요청하는 모든 주소는 테이블을 경유하므로 페이지 테이블 엔트리마다 r, w, x 비트를 두어 (read, write, execute) 해당 페이지에 대한 접근을 제어한다.

만약 rwx 가 110 인 주소에 대해서 execute 하는 CPU 실행이 일어나면, Interrupt 를 발생시켜 OS 의 서비스 루틴을 실행해서 해당 프로세스를 종료시킨다.

### 공유 (Sharing)

같은 프로그램을 사용하는 복수 개의 프로세스가 있다면, Code + data + stack 에서 code 는 공유가 가능하다.

복수 개의 프로세스들로 하여금, 페이지 테이블 코드 영역을 같은 곳을 가리키게 한다.

단 non-self-modififying-code (=reentrant code =pure code) 의 경우에만 Sharing 이 가능하다. 실행 중에 코드가 변경되면 문제가 생기기 때문이다.

## 세그멘테이션 (Segmentation)

Process 를 논리적 내용으로 자르는 것을 세그멘테이션이라고 한다.

> Paging 은 Process 를 일정 크기로 자르는 것이다.

- 프로세스는 세그먼트의 집합이다.
- 일반적으로 세그먼트의 크기는 같지 않다.
- 크게는 코드, 데이터, 스텍으로 나눌 수 있다.

세그먼테이션은 보통 code + stack + data 로 나뉘며 코드에서도 main() 루틴 서브 루틴, ... 로 나뉠 수 있다.

CPU 입장에선 프로세스가 연속된 공간에 있어야 한다. 따라서 CPU 를 속이기 위해서 MMU 내의 Relocation Register 를 이용한다.

MMU 는 Base 와 Limit 으로 이루어진 2차원 배열 Segment Table 이 된다.

CPU 가 호출하는 주소는 s (segment 번호) + d (displacement 번호, 변위)로 나뉜다.

CPU 가 요청을 하게 되면 세그먼트 번호(s) 에 해당하는 세그먼트 테이블 인덱스의 Base 값을 불러들이고 d(변위)를 더해서 물리주소를 만든다.

이때, Limit 을 넘어서면 Segment Violation Interrupt 가 발생한다.

### 예제

| Limit | Base |
| ----- | ---- |
| 1000  | 1400 |
| 400   | 6300 |
| 400   | 4300 |
| 1100  | 3200 |
| 1000  | 4700 |

- 논리주소 (2, 100) 는 물리주소 무엇인가?
- 논리주소 (1, 500) 는 물리주소 무엇인가?

첫 번째는 4400 일 것이고

두 번째는 Limit 을 초과한다.

## 보호와 공유

### Paging 과 Segmentation

프로세스를 자르는 방식으로 **페이징(paging)**과 **세그먼테이션(segmentation)**이 있다.

**페이징(paging)**은 **일정 크기**로 나누는 것이고 **세그먼테이션(segmentation)**은 **가변 크기**로 나누는 것이다.

### 보호

위에서 언급한 Paging 처럼 rwx 비트를 두어 접근을 제어한다.

Segmentation 은 Paging 보다 보호적 관점에서 보면 더 우월하다.

왜냐면 paging 을 이용하면 일정크기로 자르다 보니, 하나의 페이지가 Code 도 포함하고 Data 도 포함할 수 있다. 그래서 그 페이지에 대해서 권한 처리를 하기가 애매해진다.

### 공유

위에서 언급한 Paging 처럼, 같은 프로그램을 사용하는 복수 개의 프로세스가 있다면 code 를 공유할 수 있다.

## Segmentation External Fragmentation

페이징에선 나타나지 않던 문제지만, 세그먼트의 크기가 가변적이다 보니 **외부 단편화(External Fragmentation)**가 발생할 수 있다. 따라서 Memory Loss 가 발생한다.

따라서 메모리 활용도를 높이기 위해서 페이징을 사용하려 했고, 권한 처리를 더 효율적으로 하기 위해 Segmentation 을 사용하려 했는데, 역으로 외부 단편화가 발생하여 Memory Loss 가 생기게 되었다.

따라서 세그먼트를 페이징하는 방식으로 해결한다.

처음에는 세그먼트로 나누고 그 세그먼트를 일정 크기의 페이지로 나누는 것이다.

다시 이 방식을 사용하다보면 CPU 가 Segment Table -> Page Table 을 통과하여 2번의 Translation 을 하게되는 단점이 있다. 하지만 이또한 Trade-off 일뿐이다.

## References

http://www.kocw.net/home/search/kemView.do?kemId=978503

http://contents.kocw.or.kr/KOCW/document/2013/kyungsung/yangheejae/os04.pdf

https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/8_MainMemory.html
