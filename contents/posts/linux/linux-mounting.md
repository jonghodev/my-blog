---
title: Linux Mounting
date: 2021-10-10 11:10:97
update: 2021-10-10 11:10:97
tags:
 - linux
description:
---

## USB 마운팅하기

### 방금 꼽은 USB 블록 디바이스가 어느 경로에 있는지 찾는다.

보통 /dev/sdda1 와 같은 경로에 있다.

```bash
$ fdisk -l

Disk /dev/nvme0n1: 953.9 GiB, 1024209543168 bytes, 2000409264 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: FB97606C-6FF1-40E1-AFB0-F3F009086BF7

Device           Start        End    Sectors   Size Type
/dev/nvme0n1p1    2048    1230847    1228800   600M EFI System
/dev/nvme0n1p2 1230848    3327999    2097152     1G Linux filesystem
/dev/nvme0n1p3 3328000 2000408575 1997080576 952.3G Linux LVM

Disk /dev/mapper/cl-root: 936.6 GiB, 1005601161216 bytes, 1964064768 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

Disk /dev/mapper/cl-swap: 15.8 GiB, 16903045120 bytes, 33013760 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

### /mnt 폴더에 마운트하기

```bash
mount /dev/sdda1 /mnt
ls /mnt
```

### unmount 하기

```bash
umount /mnt
```
