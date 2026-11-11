# RISC-V Multithreaded Kernel

A small kernel with thread support for the RISC-V architecture, running on the QEMU emulator.

Made as a project for the course **Operating Systems 1**, School of Electrical Engineering, University of Belgrade (year 2023).

## Overview

The kernel and the user program are linked together into one program and share one address space, like on many embedded systems. User code runs in user mode and kernel code runs in supervisor mode. A small host system from the course loads and starts the program.

The kernel does not use any standard C/C++ libraries or any services of the host system (threads, semaphores, synchronization). Everything is written from scratch.

The host system, the libraries in `lib/` and the tests are provided by the course. The code in `src/` and `h/` is my own work.

## Features

A user program running on the kernel can:

- allocate and free memory
- create and start threads
- give up the processor to another thread
- wait for another thread to finish (join)
- use semaphores to synchronize threads
- put a thread to sleep for a given time

All of this is available through the C++ API that is provided to the user program.

## Architecture

The system is built in layers:

```
User program
     |
  C++ API      classes (Thread, Semaphore)
     |
   C API       C functions that wrap system calls
     |
    ABI        system calls
     |
  Kernel       trap handler, scheduler, allocator, threads, semaphores
```

**C++ API**: C++ classes that wrap the C functions.

**C API**: C functions that prepare the arguments and make the system call.

**ABI**: the system call itself

**Kernel**: the kernel code that does the work: trap handler, scheduler, memory allocator, threads and semaphores. It also handles the timer and console interrupts that come from the hardware.

## Implementation

- **Memory allocator**:
  - Linked list of free blocks
  - First fit allocation
  - Neighbouring free blocks are merged when memory is freed
- **Scheduler**:
  - A simple FIFO queue of ready threads
- **Thread blocking**:
  - Threads waiting on a semaphore or on a join are kept in a list and put back in the scheduler when released
- **Thread sleep**:
  - Sleeping threads are kept in a list with a counter that goes down on every timer tick
- **Interrupts**:
  - Interrupts are masked while kernel code runs, so the kernel cannot be preempted

## Technologies

C, C++, RISC-V Assembly, QEMU, GNU Make, GDB, Linux (Ubuntu)
