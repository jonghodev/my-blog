---
title: OS (6) Process
date: 2020-11-21 19:00:00
update: 2020-11-21 19:00:00
tags:
 - os
description:
---

## Program vs Process

process: program in execution

하드디스크 안에 있는 것을 Program 이라하고

이 Program 이 Main Memory 의 Ram 영역에 올라오면 그것을 Process 라고 한다.

## Process State

### Created

When a process is first created, it occupies the "created" or "new" state.

In this state, the process awaits admission to the "ready" state. Admission will be approved or delayed by a long-term, or admission, scheduler. Typically in most desktop computer system, this admission will be approved automatically. However, for real-time operating systems this admission may be delayed. In a realtime system admitting too many processes to the "ready" state may lead to oversaturation and overcontenction of the system's resources, leading to an inabillity to meet process deadlines.

### Ready

A "ready" or "waiting" process has been loaded into main memory and is awaiting execution on a CPU (to be context switched onto the CPU by the dispatcher, or short-term scheduler.)

However, the CPU is only capable of handling one process at a time. Processes that are ready for the CPU are kept in a queue for "ready" processes. Other processes that are waiting for an event to occur, such as loading information from a hard drive or waiting on an internet connection, are not in the ready queue.

### Running

A process moves into the running state when it is chosen for execution. The process's instructions are executed by one of the CPUs (or cores) of the system.

There is at most one running process per CPU or core. A process can run in either of the two modes, namely kernel mode or user mode.

**kernel mode**

- unrestricted access to hardware including execution of privileged instructions.
- Various instructions (such as I/O instructions and halt instructions) are privileged and can be executed only kernel mode
- A system call from a user program leads to a switch to kernel mode

**user mode**

- Processes in user mode can access their own instructions and data but no kernel instructions and data (or those other processes)
- When the computer system is executing on behalf of a user application, the system is in user mode. However, when a user application requests a service from operaing system (via a system call), the system must transition from user to kernel mode to fulfill the request.
- User mode avoids various catastrophic failures:
  - There is an isolated virtual address space for each process in user mode.
  - User mode ensures isolated execution of each process so that i does not affect other process as such.
  - No direct acess to any hardware device is allowed.

## Process control block (PCB)

A **process control block (PCB)** is a data structure used by computer [operating systems](https://en.wikipedia.org/wiki/Operating_system) to store all the information about a [process](<https://en.wikipedia.org/wiki/Process_(computing)>). It is also known as a process **descriptor**.

- When a process is created (initialized or installed), the operating system creates a corresponding process control block.
- Information in a process control block is updated during the transition of process states.
- When the process terminates, its PCB is returned to the pool from which new PCBs are drawn.
- Each process has a single PCB.

include process state (running, ready, waiting, ...), PC, registers, MMU info (base, limit), CPU time, process id, list of open files

## References

https://kr.wikipedia.org/wiki/Process_control_block

http://www.kocw.net/home/search/kemView.do?kemId=978503
