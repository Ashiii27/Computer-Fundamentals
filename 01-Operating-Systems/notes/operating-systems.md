# Operating Systems — Complete Revision Notes

> Covers: OS basics → Processes & Threads → CPU Scheduling → Synchronization → Deadlocks → Memory Management → File Systems → Disk & I/O

## Table of Contents
1. [Introduction to OS](#1-introduction-to-os)
2. [Processes & Threads](#2-processes--threads)
3. [CPU Scheduling](#3-cpu-scheduling)
4. [Process Synchronization](#4-process-synchronization)
5. [Deadlocks](#5-deadlocks)
6. [Memory Management](#6-memory-management)
7. [File Systems](#7-file-systems)
8. [Disk & I/O Management](#8-disk--io-management)
9. [Linux Quick Reference](#9-linux-quick-reference)
10. [One-Page Cheat Sheet](#10-one-page-cheat-sheet)

---

## 1. Introduction to OS

**Operating System**: system software that acts as an intermediary between the user and the hardware, managing resources (CPU, memory, I/O, files) and providing services to application programs.

### Functions of an OS
- **Process management** — creation, scheduling, synchronization, termination
- **Memory management** — allocation/deallocation, protection, virtual memory
- **File system management** — storage, retrieval, naming, sharing, protection
- **Device/I/O management** — buffering, caching, spooling, device drivers
- **Security & protection** — authentication, access control, isolation
- **User interface** — CLI (shell) and/or GUI

### Types of OS
| Type | Key idea | Example / Use |
|---|---|---|
| Batch | Jobs batched, no user interaction | Old mainframes, payroll |
| Time-sharing (multitasking) | CPU time-sliced among users/tasks | Unix |
| Distributed | Multiple machines appear as one | LOCUS, clusters |
| Real-time (RTOS) | Guaranteed response deadlines | Airbags, pacemakers (hard RT); streaming (soft RT) |
| Embedded | Dedicated device, minimal resources | IoT, routers |
| Mobile | Optimized for touch, battery | Android (Linux kernel), iOS |

### Kernel, Shell, System Call
- **Kernel**: core of the OS; runs in privileged (kernel) mode; directly controls hardware.
- **Shell**: command interpreter; the interface between user and kernel.
- **System call**: program's request to the kernel for a service (e.g., `fork()`, `read()`, `open()`, `exec()`, `wait()`).
  - Categories: process control, file management, device management, information maintenance, communication, protection.

### Kernel Architectures
| Type | Idea | Pros | Cons | Example |
|---|---|---|---|---|
| Monolithic | All services in kernel space | Fast (no message passing) | Huge, one bug can crash all | Linux, traditional Unix |
| Microkernel | Minimal kernel; services run in user space | Reliable, modular, secure | Slower (IPC overhead) | QNX, MINIX, Mach |
| Hybrid | Mix of both | Balance | Complexity | Windows NT, macOS (XNU) |

### Dual Mode Operation
- **User mode** (mode bit 1): unprivileged; apps run here.
- **Kernel mode** (mode bit 0): privileged instructions allowed.
- Transition user→kernel happens via system calls, interrupts, or traps.

---

## 2. Processes & Threads

### Program vs Process vs Thread
- **Program**: passive executable file on disk.
- **Process**: program **in execution**; active entity with its own address space, stack, registers, PC.
- **Thread**: unit of execution **within** a process; shares code/data/files with sibling threads but has its own **stack, registers, program counter**.

### Process States (5-state model)
```
NEW → READY ⇄ RUNNING → TERMINATED
              ↑          |
              |          ↓ (I/O or event wait)
              +---- WAITING/BLOCKED
```
- **New**: being created. **Ready**: waiting for CPU. **Running**: instructions executing.
- **Waiting/Blocked**: waiting for I/O or event. **Terminated**: finished.
- (Some models add **Suspended** — swapped out of memory.)

### PCB (Process Control Block)
Per-process kernel structure storing: PID, process state, program counter, CPU registers, scheduling info, memory info (page tables), I/O status, list of open files. Context switch = saving one PCB and loading another.

### Context Switch
- CPU switches from one process to another; kernel saves the old context in its PCB and loads the new one.
- **Pure overhead** — no useful user work during the switch; cost = time + cache/TLB flushes.

### Threads & Multithreading Models
| | User-level threads | Kernel-level threads |
|---|---|---|
| Managed by | Thread library (user space) | OS |
| Kernel aware? | No | Yes |
| Blocking one thread | Blocks whole process | Only that thread |
| Switch cost | Very cheap | Costlier (mode switch) |
| Parallelism on multicore | No | Yes |

**Mapping models**: Many-to-One, One-to-One (Linux, Windows), Many-to-Many.

### `fork()` in Unix
- Creates a child by duplicating the parent; **returns 0 to the child, child's PID to the parent, -1 on failure**.
- Followed typically by `exec()` (replace image) + `wait()` (parent waits).
- **Zombie process**: child finished, parent hasn't called `wait()` — entry stays in process table.
- **Orphan process**: parent died first; child is re-adopted by `init` (PID 1).

### IPC (Inter-Process Communication)
| Mechanism | Notes |
|---|---|
| Shared memory | Fastest; needs synchronization (e.g., producer–consumer buffer) |
| Message passing / message queues | Kernel-mediated; good for distributed |
| Pipes | Unidirectional; named pipes (FIFOs) work between unrelated processes |
| Sockets | Bidirectional, works across machines |
| Signals | Async notification (SIGKILL, SIGINT…) |

### Critical Section Problem
Code segment where shared resources are accessed. A correct protocol must ensure:
1. **Mutual exclusion** — only one process inside CS at a time
2. **Progress** — no unnecessary blocking of others
3. **Bounded waiting** — limit on how long a process waits

*Peterson's Solution*: software solution using `flag[]` + `turn` variable; satisfies all three (on modern CPUs needs memory barriers).

---

## 3. CPU Scheduling

### Key Metrics
- **CPU utilization** — keep CPU busy
- **Throughput** — processes completed per unit time
- **Turnaround time (TAT)** = Completion − Arrival
- **Waiting time (WT)** = TAT − Burst time
- **Response time** = first time CPU given − arrival (matters for interactive)

### Preemptive vs Non-preemptive
- **Non-preemptive**: process keeps CPU until it terminates or blocks (FCFS, SJF).
- **Preemptive**: OS can take CPU back (SRTF, Round Robin, Priority-preemptive). Better interactivity; more overhead.

### Algorithms
| Algorithm | Type | Idea | Problem |
|---|---|---|---|
| **FCFS** | Non-preemptive | Run in arrival order | **Convoy effect** (short jobs stuck behind long one); high avg waiting time |
| **SJF** | Non-preemptive | Shortest burst first | Optimal avg waiting time; needs burst prediction; **starvation** of long jobs |
| **SRTF** | Preemptive | SJF with preemption when a shorter job arrives | Same issues; best avg WT among preemptive |
| **Priority** | Both | Highest priority first | **Starvation** → fixed by **aging** (gradually raise priority of waiting processes) |
| **Round Robin** | Preemptive | FCFS + time quantum; cyclic | Fair, good response time; performance depends on quantum |
| **MLQ** | — | Separate queues per class; queue priorities | Rigid |
| **MLFQ** | — | Multiple queues with priorities; jobs move between queues (aging) | Most complex; approximates SJF without prediction; used in real systems |

**Time quantum tuning**: too large → FCFS behavior; too small → context-switch overhead dominates (rule of thumb: 80% of bursts < quantum).

---

## 4. Process Synchronization

### Race Condition
Result depends on the order of execution of concurrent processes accessing shared data. Prevent by making shared-data manipulation **atomic**.

### Hardware Support
- Disable interrupts (uniprocessor only) — not scalable.
- Atomic instructions: **Test-and-Set**, **Compare-and-Swap** — basis of hardware locks.

### Mutex vs Semaphore vs Spinlock
| | Mutex (lock) | Binary semaphore | Counting semaphore | Spinlock |
|---|---|---|---|---|
| Value | locked/unlocked | 0/1 | 0..N | locked/unlocked |
| Ownership | Yes (only locker can unlock) | No | No | Yes |
| Purpose | Mutual exclusion | Signaling / ordering | Resource pools (N instances) | Short waits |
| Waiting | Blocks (sleep) | Blocks | Blocks | **Busy-waits** (burns CPU) |

- **Semaphore operations**: `wait()/P()/down()` (decrement, block if < 0) and `signal()/V()/up()` (increment, wake one).
- **Spinlocks** are OK on multiprocessors when the wait is very short (avoid context-switch cost).

### Classic Problems (know the semaphore counts)
1. **Producer–Consumer (Bounded Buffer)** — `empty = N`, `full = 0`, `mutex = 1`.
2. **Readers–Writers** — shared read count + mutex, writer mutex; variants favor readers/writers/fairness.
3. **Dining Philosophers** — 5 philosophers, 5 forks; deadlock possible if all pick left fork. Fixes: allow only 4 at the table, pick both forks atomically, odd/even ordering, use a waiter (monitor/semaphore).

### Monitors
High-level construct: shared data + procedures + mutual exclusion built-in; condition variables with `wait()`/`signal()`. Java `synchronized` methods implement the monitor idea.

---

## 5. Deadlocks

**Deadlock**: a set of processes each waiting for a resource held by another member of the set — nobody can proceed.

### Four Necessary Conditions (Coffman conditions) — ALL must hold
1. **Mutual exclusion** — resource usable by one process at a time
2. **Hold and wait** — holding some resources while waiting for more
3. **No preemption** — resources can't be forcibly taken
4. **Circular wait** — P1→P2→…→Pn→P1 cycle of waiting

Break any one condition to prevent deadlock (e.g., request all resources upfront kills hold-and-wait; impose total ordering of resources kills circular wait).

### Handling Strategies
| Strategy | Idea | Cost |
|---|---|---|
| **Prevention** | Design so a Coffman condition can't hold | Low utilization |
| **Avoidance** | Grant requests only if system stays in a **safe state** — **Banker's Algorithm** (needs max claims declared in advance) | Runtime checks |
| **Detection + Recovery** | Wait-for graph / RAG cycle detection; then kill processes or preempt resources | Detection cost, lost work |
| **Ostrich algorithm** | Ignore it (Linux/Windows mostly do) | Rare crashes |

**Resource Allocation Graph (RAG)**: cycle exists ⇒ deadlock if single instance per resource type; with multiple instances a cycle is only *necessary*, not sufficient.

---

## 6. Memory Management

### Contiguous Allocation
- **First fit** — first hole big enough (fast, generally good)
- **Best fit** — smallest adequate hole (creates tiny useless fragments)
- **Worst fit** — largest hole (leaves big leftover pieces)

**Fragmentation**
| Internal | External |
|---|---|
| Wasted space *inside* an allocated block (block > request) | Free memory scattered in small non-contiguous holes; total free is enough but unusable |
| Fixed-size partitioning, paging | Variable partitioning |
| Fix: better block sizing | Fix: **compaction**, or **paging** |

### Paging
- Divide logical memory into fixed-size **pages** and physical memory into same-size **frames** (typically 4 KB).
- **Page table** maps page number → frame number; logical address = (page, offset).
- **No external fragmentation**; small **internal fragmentation** in last page.
- **TLB (Translation Lookaside Buffer)**: associative cache of recent page-table entries; makes address translation fast (else 2 memory accesses per reference).
- Multi-level page tables reduce the memory needed for the table itself.

### Segmentation
- User-visible logical units: code, stack, heap, functions. Each segment has **base + limit**.
- Suffers external fragmentation (variable sizes), but matches programmer's view.
- Combined systems: segmentation with paging (e.g., x86 historically, IA-32).

| Paging | Segmentation |
|---|---|
| Fixed-size blocks | Variable-size logical units |
| Invisible to programmer | Visible to programmer |
| Internal fragmentation | External fragmentation |
| One linear address space | Independent segments |

### Virtual Memory
- Run processes with only **some pages in RAM**; the rest on disk (swap).
- Benefits: programs larger than RAM, more multiprogramming, faster process creation (copy-on-write), memory protection & sharing.
- **Demand paging**: load a page only when referenced (lazy loading). Invalid/fault bit triggers **page fault** → OS finds the page on disk → frame allocation → disk read → update page table → restart instruction.

### Page Replacement Algorithms
| Algorithm | Idea | Notes |
|---|---|---|
| **FIFO** | Evict oldest loaded page | Suffers **Belady's anomaly** (more frames can mean MORE faults) |
| **Optimal (OPT/MIN)** | Evict page not needed for the longest future time | Not implementable (needs future knowledge); benchmark |
| **LRU** | Evict least recently used | Stack algorithm → no Belady's anomaly; approximated by reference bits / clock (second-chance) algorithm |
| **Clock / Second chance** | Circular FIFO + reference bit | Practical LRU approximation |
| LFU / MFU | Frequency-based | Rarely used |

**Effective access time** = (1 − p)·mem_access + p·page_fault_time — keep fault rate tiny.

### Thrashing
- Process spends more time paging than executing (fault rate explodes). CPU utilization drops while disk is saturated.
- **Cause**: too little memory per process / over-committed memory.
- **Fixes**: working-set model (give each process enough frames for its working set), page-fault frequency control, reduce multiprogramming degree, add RAM.

**Locality of reference**: temporal (recently used → soon again) and spatial (nearby addresses → soon) — what makes caching & virtual memory work.

---

## 7. File Systems

- **File**: named collection of related information + attributes (name, size, permissions, timestamps).
- **Access methods**: sequential, direct (random), indexed.
- **Directory structures**: single-level → two-level → **tree** → acyclic graph (links) → general graph.
- **Allocation methods**:
  | Method | Pros | Cons |
  |---|---|---|
  | Contiguous | Fast sequential & direct read | External fragmentation; hard to grow |
  | Linked | No external fragmentation; easy growth | Slow random access; pointer overhead (FAT fixes this) |
  | Indexed | Direct access via index block | Index block overhead (UNIX inode = multi-level index: direct + single/double/triple indirect) |
- **Free-space management**: bitmap/bit-vector, linked list, grouping.
- **Mounting**: attaching a file system to a directory (mount point) of the existing tree.
- **Journaling**: log changes before applying → crash consistency (ext4, NTFS).

---

## 8. Disk & I/O Management

### I/O Techniques
1. **Programmed I/O** — CPU busy-waits/polls the device.
2. **Interrupt-driven I/O** — device interrupts CPU when ready.
3. **DMA (Direct Memory Access)** — device transfers block directly to memory; CPU interrupted once per block. Used for high-speed I/O.
- **Buffering** (smooth speed mismatch), **caching** (fast copies), **spooling** (queue for devices that can't interleave jobs — e.g., printer).

### Disk Structure & Scheduling
- Disk = platters → tracks → sectors; seek time + rotational latency + transfer time.
- **Seek time** is the dominant cost → scheduling minimizes head movement.

| Algorithm | Idea | Problem |
|---|---|---|
| FCFS | Serve in arrival order | Poor throughput |
| SSTF | Nearest request next | Starvation of far requests |
| SCAN (elevator) | Sweep end-to-end serving on the way | Requests at far edge wait |
| C-SCAN | Sweep one direction only, jump back | More uniform wait |
| LOOK / C-LOOK | SCAN/C-SCAN but reverse at last request (don't hit the end) | Practical default |

### RAID (Redundant Array of Inexpensive Disks)
| Level | Technique | Fault tolerance | Min disks | Use |
|---|---|---|---|---|
| 0 | Striping | None | 2 | Speed only |
| 1 | Mirroring | 1 disk per mirror | 2 | OS disks, critical data |
| 5 | Striping + distributed parity | 1 disk | 3 | Read-heavy servers |
| 6 | Double parity | 2 disks | 4 | Large arrays |
| 10 (1+0) | Mirror + stripe | Up to 1 per mirror | 4 | Databases, write-heavy |

---

## 9. Linux Quick Reference

- Kernel → shell → applications; everything is a **file** (devices in `/dev`).
- Commands: `ps`, `top`/`htop` (processes), `kill` (signals: SIGTERM 15, SIGKILL 9), `free`, `df`/`du` (disk), `vmstat`, `nice`/`renice` (priority), `chmod`/`chown` (permissions), `systemctl` (services).
- Permissions: `rwx` for user/group/other; `755` = rwxr-xr-x.
- Signals: async notifications (`Ctrl+C` = SIGINT); SIGKILL/SIGSTOP can't be caught.

---

## 10. One-Page Cheat Sheet

| Concept | One-liner |
|---|---|
| System call | User → kernel gateway (`fork`, `exec`, `read`, `write`) |
| Context switch | Save PCB A, load PCB B; pure overhead |
| fork() return | 0 → child, PID → parent |
| Zombie | Dead child not reaped by parent's `wait()` |
| Convoy effect | FCFS: short jobs stuck behind long ones |
| Aging | Fix for starvation in priority scheduling |
| Semaphore | int with atomic wait/signal; counting vs binary |
| Mutex vs binary sem | Mutex has ownership; sem is for signaling too |
| Deadlock | 4 conditions: mutual excl., hold&wait, no preemption, circular wait |
| Banker's | Deadlock **avoidance** via safe states |
| Paging | Fixed pages→frames; no external frag; needs TLB |
| Belady's anomaly | FIFO: more frames ⇒ more faults possible |
| LRU | Stack algorithm; no anomaly; clock approximates it |
| Thrashing | Paging more than executing; fix = working set |
| Optimal replacement | Evict page unused for longest future — theoretical best |
| SCAN | Elevator: sweep, serve on the way |
| RAID 5 | Striping + distributed parity, survives 1 disk |
| DMA | Device↔memory transfer without CPU per word |
