
# Round-Robin Scheduling
## Overview

**Round Robin:**
Round-Robin is one of the most common scheduling policies and serves as the foundation of time-slicing. It is basically a timeslice rotation policy. Each thread is granted an equal timeslice, a fixed quantum of CPU time.

**QNX Round Robin Example:**
QNX uses a priority-based preemptive scheduler. The highest-priority ready thread always runs first. Round-Robin is not the scheduler itself - it is a policy that governs how threads **at the same priority level share CPU time.**

## How It Works

![[Pasted image 20260530084348.png]]
`<Image Credit : QNX>`

1. All threads **at the same priority level**
   They receive an identical timeslice. Within a priority band, scheduling is fair - no thread gets more CPU time than its peers.
2. When a thread's timeslice expires, it releases the CPU and moves to the back of the ready queue. The next thread in the same priority queue is then dispatched.
3. If a thread blocks (e.g., waiting on I/O) before its timeslice expires, it relinquishes the CPU early.
## Queue Structure

The scheduler maintains a **separate ready queue** for each priority level (0–255 in QNX). **Dispatch always starts from the highest non-empty priority queue.** Round-Robin rotation only occurs among threads within the same queue.

```
The higher the number, the higher the importance

Priority 2: [Thread A] → [Thread B] # RR rotation here
Priority 1: [Thread D]              # Only runs when P2 is empty
```

## Timeslice Size (in QNX)

```
Timeslice = 4 x tick
```

With the default tick of 1 ms, the timeslice is **4 ms**. This is a tuned trade-off between responsiveness (shorter slice) and efficiency (fewer context switches).