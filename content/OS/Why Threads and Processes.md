## Core Definitions: Threads and Processes

* **Thread:** The smallest unit of scheduling and execution dispatched to a CPU core by the OS scheduler. While it shares resources with its parent process, it maintains an independent execution flow by having its own **PC (Program Counter), Stack, and Register Set**.
* **Process:** The primary entity that owns an Address Space. It holds exclusive rights to system resources - such as the `Code`, `Data`, and `Heap` segments, along with File Descriptors - and shares them among its internal threads. A process consists of one or more threads.

## Why threads and processes?
- **Background:** Relying solely on processes to divide tasks is heavy.
	- A process [[Understanding Context Switch, Processes vs. Threads|context switch]]: It incurs massive overhead because it requires completely swapping the virtual memory mappings.
	- sharing data between processes: It necessitates passing through complex Inter-Process Communication (IPC) mechanisms.
- **Solution:** Consolidate the heavy resource management (memory, files) into a single container, and split off multiple lightweight execution units. This is why modern architectures separate the **Process** (the owner of resources) from the **Thread** (the unit of execution).

## Concurrency and Resource Synchronization
- The Need for Concurrency: While threads can operate independently, **they are typically executed concurrently** to maximize system parallelism and overall performance.
- The Risk of Race Conditions: Because threads share the process's common memory space (such as the Data and Heap), simultaneous attempts to access and modify the same shared resource can trigger a **Race Condition.**
- Synchronization: To guarantee data consistency, it is crucial to control access to these shared resources using standard OS-provided synchronization mechanisms, such as **[[The Essence of Mutex|a Mutex]] or a Semaphore.**
