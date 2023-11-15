---
title: Linux Swap Memory
date: 2020-09-20 12:30:00
update: 2020-09-20 12:30:00
tags:
 - linux
description:
---

## Swap Memory 란

리눅스는 가상 메모리(virtual memory)란 것을 지원한다. 메모리 사용량이 늘어남에 따라,
디스크의 일부를 마치 확장된 RAM 처럼 사용할 수 있께 해주는 기술이다.
[출처](<https://3dmpengines.tistory.com/1663#:~:text=%EB%A6%AC%EB%88%85%EC%8A%A4%EB%8A%94%20%EA%B0%80%EC%83%81%20%EB%A9%94%EB%AA%A8%EB%A6%AC(virtual%20memory)%EB%9E%80%20%EA%B2%83%EC%9D%84%20%EC%A7%80%EC%9B%90%ED%95%9C%EB%8B%A4.&text=%EC%9D%B4%EB%A0%87%EB%93%AF%20%EA%B0%80%EC%83%81%EC%A0%81%EC%9D%B8%20%EB%A9%94%EB%AA%A8%EB%A6%AC%EB%A1%9C,%EB%B0%94%EA%BF%94%EC%B9%98%EA%B8%B0%EB%A5%BC%20%ED%95%9C%EB%8B%A4%EB%8A%94%20%EB%9C%BB>)

## Swap Memory 역할

Ram 에 발생하는 부하를 줄여주기 위한 것이다.
디스크 IO 는 RAM 비해 훨씬 느리지만, 램을 모두 사용했을 때 시스템에 문제가 생기는 것을 대비한다.
여러 Proccess 들에 의해 free memory 수준이 특정 임계값 이하로 떨어지게 되면, 기존 프로세스의 일부나 새롭게 읽어드리는 프로세스를 위해
디스크 상의 Swap memory 를 사용해 free memory 영역을 확보해 주는 것이다.
Swap in, Swap out 은 성능을 많이 저하시키니 가능한 Free memory 선에 처리되는 것이 좋다.
[출처](https://spr2ad.tistory.com/130)

## Radhat 의 Recommended System Swap Space

> 아래는 Radhat 의 Recommended System Swap Space 이다. [출처](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-swapspace)

| Amount of RAM in the system | Recommended swap space     | Recommended swap space if allowing for hibernation |
| --------------------------- | -------------------------- | -------------------------------------------------- |
| ⩽ 2 GB                      | 2 times the amount of RAM  | 3 times the amount of RAM                          |
| > 2 GB – 8 GB               | Equal to the amount of RAM | 2 times the amount of RAM                          |
| > 8 GB – 64 GB              | At least 4 GB              | 1.5 times the amount of RAM                        |
| > 64 GB                     | At least 4 GB              | Hibernation not recommended                        |

## Swap Memory 와 Swap Partition

스왑 영역을 생성하기 위해 파일을 사용할 수 있고 파티션을 사용할 수 있다.
스왑 파티션은 속도가 빠른 반면, 스왑 파일은 그 크기를 자유롭게 조절할 수 있다.
따라서 스왑 영역이 얼마나 필요한지 알고 있다면 스왑 파티션을 사용하고 모른다면 스왑 파일을 생성하면 된다.
만약 얼마나 필요할지 모를경우 우선 스왑파일을 만들어 필요한 공간이 얼마인지 확인 후 스왑 파티션을 잡으면 된다.

또한 리눅스에서는 여러 개의 스왑 파티션과 스왑 파일을 섞어 사용할 수 있다.
이 방법을 이용해 언제나 큰 용량의 스왑 영역을 잡을 필요 없이 그때 그때 필요한 만큼의 스왑을 늘려줄 수 있으므로 편리하다.
[출처](https://spr2ad.tistory.com/130)

## Swap 확인

swapon -s : 스왑이 잡혀 있지 않으면 아무것도 출력되지 않는다.

```bash
swapon -s
```

free -m : Swap 항목의 모든 결과가 0으로 출력 된다.

```bash
free -m
```

## Swap 적용

```bash
# swap 파일을 저장할 폴더를 생성한다.
mkdir /swap

# dd 을 이용해 (2GB) 의 Swap 파일을 생성한다. (블럭 1M \* 2049 = 2GB)
dd if=/dev/zero of=/swap/swapfile bs=1M count=2048

# 위에서 만든 단순 data 파일을 swap file 로 초기화 시킨다.
mkswap /swap/swapfile

# 권한을 파일 소유자로 제한한다.
chmod 600 /swap/swapfile

# 스왑 파일을 시스템이 사용할 수 있도록 스왑 영역으로 활성화한다.
swapon /swap/swapfile
```

## Swap 재부팅 후에도 적용

시스템이 재부팅되면 스왑영역이 비활성화 되므로 swapon 명령을 통해 활성화를 해줘야하는 번거로움이 있다.
재부팅 시 자동으로 스왑 영역을 활성화 되도록 /etc/fstab 에 등록한다.
[출처](https://devanix.tistory.com/311)

```bash
vi /etc/fstab
/swap/swapfile swap swap defaults 1 1
```

## Swap 생성 확인 및 삭제

스왑이 잘 생성되었는지 확인하기 위해 2가지 명령어를 사용해 확인할 수 있다.

```bash
# (1) swapon
swapon -s

# (2) free
free -h
```

스왑 영역으로 비활성화 하기 위해 swappoff 명령을 사용하고
rm 명령으로 해당 데이터 파일을 삭제할 수 있다.

```bash
swapoff /swap/swapfile
rm /swap/swapfile
```
