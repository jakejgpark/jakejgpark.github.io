# Overview
A number of embedded devices use Linux since it's free. Yet in the automotive industry, especially in safety-critical domains, BlackBerry's QNX remains the standard despite its licensing cost. The two fundamental advantages that justify this choice are `Determinism` and `Isolation`.

## Determinism
An autonomous driving system follows a Perceive → Decide → Control.
The most critical requirement is a strict deadline to ensure safety.

QNX achieves this through several mechanisms:
- Priority Preemption: While Linux uses **the Completely Fair Scheduler**, which optimizes for fairness across tasks, QNX uses **a priority-based preemptive scheduler.**
- Priority Inheritance: This prevents **Priority Inversion.** When a low-priority task holds a resource that a high-priority task needs, the low-priority task temporarily inherits the higher priority. 
- Bounded Latency: The system provides mathematically guarantees on **Worst-Case Execution Time (WCET).**

## Isolation
Linux uses a Monolithic Kernel architecture - all device drivers resides in kernel space. A single driver bug can cause kernel panic.

QNX takes the opposite approach with a Microkernel architecture:
- Memory Protection: Every service and device driver runs **as an independent process** in user space, each with its own address space.
- Fault Containment: Such as camera driver and the brake control process - running in a completely separate address space is unaffected. The only failed process is simply restarted.
- Adaptive Partitioning: CPU time is strictly allocated per partition. Even if a faulty process attempts to consume 100% of CPU, other partitions are guaranteed their minimum allocated execution time.