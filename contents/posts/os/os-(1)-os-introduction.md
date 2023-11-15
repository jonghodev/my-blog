---
title: OS (1) OS Introduction
date: 2020-11-16 07:00:00
update: 2020-11-16 07:00:00
tags:
 - os
description:
---

## 1. OS 란?

운영체제(Operating System)란 하드웨어 자원들을 관리하고 프로그램을 지원해주는 것이다.

## 2. OS 의 목적

### 성능 향상

과거의 MsDos 랑 Windows10 과의 속도는 천지차이다.

### 사용자 편의성

어린아이도 학습하면 컴퓨터를 사용할 수 있다.

## 3. Kernal 이란?

Kernel은 본질적으로 프로그램이다. OS 안에 있으며 사용자는 Shell 을 통해 Kernel 과 상호작용할 수 있다.

하지만 다른 모든 프로그램과 다르게 커널만이 가지고 있는 특별한 점이 있다. 그것은 바로 **Memory Resident** 하다는 점이다.

항상 메모리에 상주하는 것이 바로 커널이다. 언제부터 커널이 메모리에 상주될까?

우리는 보통 컴퓨터를 키면 OS 가 보인다. 그러면 그 사이에 일어나지 않을까? 즉 컴퓨터가 켜지면 OS 가 Memory에 올라오고 그때서야

우리는 컴퓨터가 켜졌다고 인식하고 프로그램을 실행하는 둥 컴퓨터를 사용하는 것이다.

## 4. Main Memory 란?

- Ram 과 Rom 으로 나누어진다.
- Ram은 휘발성이다.
- Rom (Read only memory)은 휘발성이 아니다.
- Rom 은 용량이 작고, 대부분 Ram 이 용량을 차지한다.

## 5. Rom 이란?

컴퓨터 부팅 시 Rom 의 코드(Instruction)를 먼저 읽어온다.

Rom 에 있는 프로그램에 대해 알아보자

- **POST** (Power-On Self-Test)
  환경 설정을 테스트한다.
  메인 메모리와 하드디스크 용량, 키보드, 마우스 꽂힘 여부 같은 것을 확인한다.
- **Boot Load** (적재)
  OS 를 하드 디스크에서 찾아 Main Memory 의 Ram 영역에 전달한다.
  이때 OS 가 Main Memory 에 올라온다.
- 그 후로는 실행될게 없어서 역할이 끝난다.

OS 가 메인 메모리에 올라오면 화면이 나타날 것이다.

OS 는 다른 프로세스와 다르게, 전기를 끄지 않는 이상 Main Memory 에 항상 **상주(Resident)**한다.

## 6. 컴퓨터 구조

![](./images/2020-11-16-os.png)

OS 는 하드웨어를 관리한다. 그리고 실질적으로 하드웨어를 관리하는 것은 Kernel 이다.

우리가 주로 공부해야할 것이 Kernel 이고 우리는 Shell 을 통해 Kernel 과 상호 작용을 하며 하드웨어를 조작한다.

> OS = kernel + shell

OS: **Process Management, Memory Management**, IO Management, File Management, Network Management

## 7. 정리

OS 는 시스템의 성능을 향상시키고 사용자 편의성을 증가 시킨다. Main Memory 는 대부분의 용량을 차지하는 Ram 과 적은 용량을 차지하는 Rom 이 있다. 컴퓨터가 켜질 때, 이 Rom 에서 POST 라는 작업을 실행함으로써 Main Memory, Disk Storage, Keyboard 같은 것을 테스트 한다. 그리고나서 Boot Load 를 통해 OS 를 하드 디스크에서 찾아 Main Memory 의 Ram 에 적재 시킨다. 이후로는 할 일이 없어서 역할이 끝난다. OS 는 Main Memory 에 항상 상주한다.

## References

http://www.kocw.net/home/search/kemView.do?kemId=978503
