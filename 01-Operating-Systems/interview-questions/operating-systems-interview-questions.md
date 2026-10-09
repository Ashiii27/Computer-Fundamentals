# Operating Systems — Top 30 Interview Questions (with Answers)

> Classic OS questions asked in campus placements and product-company interviews, ordered roughly from basics to advanced. For numericals (scheduling, Banker's, page faults) practice on paper — links in the [resources](../resources/operating-systems-resources.md).

---

### 1. What is an operating system? What are its main functions?
System software that manages hardware resources and provides services to applications. Functions: process management (scheduling, creation/termination), memory management (allocation, virtual memory), file system management, device/I/O management, security & protection, and providing a user interface (shell/GUI).

### 2. Difference between a program and a process?
A **program** is a passive executable file stored on disk. A **process** is that program *in execution* — an active entity with a program counter, registers, stack, and its own address space. One program can spawn many processes (e.g., multiple Chrome windows).

### 3. Process vs thread?
| Process | Thread |
|---|---|
| Independent address space | Lives inside a process; shares its code, data, open files |
| Heavy creation/switch (context switch + memory) | Lightweight creation/switch |
| Inter-process communication needed (pipes, sockets) | Communicates via shared variables (needs locks) |
| One crash doesn't kill others | One bad thread can corrupt the whole process |

### 4. What is a context switch? Why is it costly?
The kernel saves the state (PC, registers, memory mapping) of the current process in its PCB and restores another process's state. It's costly because no useful user work happens during it, and it flushes CPU caches/TLB, hurting performance.

### 5. Explain the process state diagram.
New → Ready (admitted) → Running (dispatched) → Terminated (exit). Running can go back to Ready (interrupt/quantum expiry) or to Waiting (I/O request); Waiting returns to Ready when the I/O completes. A running process can spawn children via `fork()`.

### 6. What does fork() return? What are zombie and orphan processes?
`fork()` duplicates the calling process: returns **0 in the child**, the **child's PID in the parent**, −1 on failure.
- **Zombie**: child exited but parent hasn't called `wait()` — its entry lingers in the process table.
- **Orphan**: parent died first; child is adopted by `init` (PID 1).

### 7. What are the IPC mechanisms?
Shared memory (fastest, needs synchronization), message queues, pipes (unidirectional; FIFOs for unrelated processes), sockets (bidirectional, cross-machine), and signals (async notifications).

### 8. What is the critical section problem?
A section of code accessing shared resources that must not be executed by more than one process at once. Any solution must guarantee: **mutual exclusion**, **progress** (no unnecessary blocking), and **bounded waiting** (no starvation).

### 9. Mutex vs semaphore?
A **mutex** is a lock with *ownership* — only the thread that locked it can unlock it; used purely for mutual exclusion. A **semaphore** is a counter with atomic `wait`/`signal`; a **binary semaphore** (0/1) can act as a lock but has no ownership (any thread can signal), and a **counting semaphore** manages N identical resources (e.g., 5 DB connections). Semaphores are also used for signaling/ordering between threads.

### 10. What is a spinlock? When is it preferable?
A lock where the waiting thread **busy-waits** in a loop instead of sleeping. Preferable on multiprocessors when the expected wait is very short — it avoids the cost of a context switch. Bad for long waits (wastes CPU) and useless on single-CPU systems.

### 11. Explain the producer–consumer (bounded buffer) problem.
Producers add items to a finite buffer; consumers remove them. Classic semaphore solution: `mutex = 1` (buffer access), `empty = N` (free slots), `full = 0` (filled slots). Producer: `wait(empty); wait(mutex); add; signal(mutex); signal(full);` — consumer symmetric. Order matters: waiting on `mutex` before `empty/full` can deadlock.

### 12. What is the dining philosophers problem? How do you fix the deadlock?
5 philosophers, 5 forks between them; each needs 2 forks to eat. If all pick up their left fork simultaneously → circular wait → deadlock. Fixes: (a) allow at most 4 philosophers to sit at the table, (b) pick up both forks atomically, (c) odd-numbered philosophers grab left, even grab right (breaks circularity), (d) central waiter/monitor.

### 13. What is a deadlock? State the four necessary conditions.
A set of processes each waiting for a resource held by another in the set — no one proceeds. All four must hold simultaneously: **mutual exclusion, hold and wait, no preemption, circular wait**.

### 14. Deadlock prevention vs avoidance vs detection?
- **Prevention**: design away one of the four conditions (e.g., request everything upfront → no hold-and-wait; resource numbering → no circular wait).
- **Avoidance**: grant a request only if the system stays in a **safe state** — Banker's algorithm (requires advance knowledge of max needs).
- **Detection & recovery**: periodically find cycles in the wait-for graph; recover by terminating processes or preempting resources.
- In practice, general-purpose OSes mostly ignore it (ostrich approach); DBs detect and roll back.

### 15. Explain the Banker's algorithm.
A deadlock-avoidance algorithm. Each process declares its maximum resource needs. For every request, the system checks whether the resulting state is **safe** — i.e., some sequence of processes can finish with available + released resources. If safe → grant; else → make the process wait. It avoids deadlock but costs bookkeeping and under-utilizes resources.

### 16. FCFS vs SJF vs Round Robin — compare. What is the convoy effect?
FCFS: arrival order, simple, non-preemptive; long jobs delay short ones (**convoy effect**). SJF: shortest burst first — provably minimal average waiting time, but needs burst prediction and starves long jobs. Round Robin: FCFS + time quantum, preemptive; fair with great response time; quantum too small → context-switch overhead, too large → degenerates to FCFS.

### 17. Preemptive vs non-preemptive scheduling?
Non-preemptive: a process keeps the CPU until it terminates or blocks (FCFS, SJF) — simple, but bad for interactivity. Preemptive: the scheduler can take the CPU away (RR, SRTF, priority-preemptive) — better responsiveness, slightly more overhead. Real-time and interactive systems are preemptive.

### 18. Define turnaround, waiting, and response time.
- **Turnaround** = completion time − arrival time (total time in system).
- **Waiting** = turnaround − burst time (time spent in ready queue).
- **Response** = time from arrival until *first* CPU response (critical for interactive systems).

### 19. What is starvation? How is it fixed?
A process waits indefinitely because others (e.g., higher priority ones) keep being chosen. Fixed by **aging** — gradually increasing the priority of waiting processes — or by fair schemes like MLFQ with promotion.

### 20. Internal vs external fragmentation?
**Internal**: wasted space *inside* an allocated block (fixed-size allocation gives you more than you asked) — happens in paging/fixed partitions. **External**: total free memory is enough but scattered in non-contiguous pieces — happens in variable partitioning and segmentation. Fixes: better sizing for internal; compaction or paging for external.

### 21. Paging vs segmentation?
Paging splits memory into fixed-size pages/frames with a page table — invisible to the programmer, no external fragmentation, but internal fragmentation in the last page. Segmentation splits memory into variable-size logical units (code, stack, heap) with base+limit — matches the programmer's view but causes external fragmentation. Many systems combine both.

### 22. What is the TLB and why do we need it?
The Translation Lookaside Buffer is a small associative cache of recent page-table entries. Without it, every memory reference needs 2 physical accesses (page table + data). With a TLB hit, translation is nearly free; hit ratios of 99%+ are typical, making effective access time close to one access.

### 23. What is virtual memory and why do we need it?
A technique giving each process the illusion of a large, private address space while only parts of it live in RAM (rest on disk via swapping). Benefits: run programs larger than physical memory, higher multiprogramming, isolation/protection, cheap process creation (copy-on-write), and only loading used pages (demand paging).

### 24. Walk through what happens on a page fault.
1. MMU finds the page's valid bit clear → trap to OS. 2. OS verifies the reference is legal. 3. Find a free frame (or run page replacement to evict a victim; if dirty, write it to disk). 4. Read required page from disk into the frame. 5. Update page table (valid bit set). 6. Restart the faulting instruction — which now succeeds.

### 25. Compare FIFO, LRU, and Optimal page replacement. What is Belady's anomaly?
FIFO evicts the oldest page — simple but ignores usage, and shows **Belady's anomaly**: increasing frames can *increase* faults. Optimal evicts the page needed farthest in the future — provably minimal, but unimplementable (needs future knowledge); used as a benchmark. LRU evicts the least recently used — a good practical heuristic (locality), and as a *stack algorithm* never shows Belady's anomaly; real systems approximate it with the clock/second-chance algorithm.

### 26. What is thrashing? How do you fix it?
When a process (or the whole system) pages in and out so much that CPU utilization collapses — the disk is saturated with paging, not useful work. Causes: too many processes for the available RAM. Fixes: **working-set model** (allocate each process frames covering its recent page usage), page-fault-frequency control, reduce degree of multiprogramming, add memory.

### 27. What is a kernel? Monolithic vs microkernel?
The kernel is the core of the OS running in privileged mode. A **monolithic kernel** puts all services (filesystem, drivers, networking) in kernel space — fast, but one bug can crash everything (Linux). A **microkernel** keeps only IPC, scheduling, and basic memory management in the kernel; services run as user-space servers — more reliable/secure but slower due to IPC (QNX, MINIX). Windows NT and macOS are hybrids.

### 28. What is a system call? Give examples and explain the flow.
The programming interface through which a user program requests a kernel service. Examples: `fork()/exec()/exit()` (process), `open/read/write/close` (files), `socket/send/recv` (network), `kill` (signals). Flow: library wrapper puts the call number + args in registers → trap instruction switches to kernel mode → kernel dispatches to the handler → result returned, mode switches back to user.

### 29. What are the common RAID levels?
RAID 0 — striping, speed only, no redundancy. RAID 1 — mirroring, survives one disk per mirror pair. RAID 5 — striping + distributed parity, survives one disk failure (min 3 disks). RAID 6 — double parity (min 4). RAID 10 — mirror then stripe, best write-heavy fault tolerance (min 4).

### 30. Name disk scheduling algorithms and why they exist.
Seek time dominates disk cost, so scheduling minimizes head movement: **FCFS** (arrival order), **SSTF** (closest request; can starve), **SCAN** (elevator — sweep back and forth), **C-SCAN** (one-direction sweep for uniform wait), **LOOK/C-LOOK** (sweep only to the last request, not the physical end).

---

## How to answer OS questions well
- Lead with a one-line definition, then contrast (tables help), then a real-world example.
- For scheduling/Banker's/page-replacement numericals: show the Gantt chart / step table — interviewers grade your *process*.
- Drop the right terminology (safe state, working set, stack algorithm, convoy effect) — it signals depth.
