### What is a Context Switch?

A context switch refers to the transition of **the execution flow from one process or thread to another.**

#### Then, what exactly is **a context?**
It refers to the values of the SRAM (register) data physically located within the CPU core. The context at a specific point in time is defined by the following hardware resources:
- **PC (Program Counter):** Indicates **the current execution point** in the code.
- **Register Set:** Holds **the temporary register data** being processed.
- **SP (Stack Pointer):** Points **to the location in the stack where function calls and local variables are stored.**
- **Others:** CR3, Status Register, etc.

#### **About a context switch**
A context switch occurs when **an interrupt (Timer, I/O, etc.) or scheduler preemption happens** while a program is running, causing the CPU to pause its current work and execute another task.

### Mechanism: Context Save & Restore
Because the CPU has only a single set of registers, **Context Save and Context Restore** are required to preserve the existing context.

- **Context Save:** Safely dumps and saves the current state of the **CPU registers to a specific memory area** (Process Control Block, PCB, or Thread Control Block, TCB).
    
- **Context Restore:** Retrieves **the saved context of the next process or thread from memory and restores it back** into the CPU registers.

In modern CPU architectures, the actual swapping of registers happens rapidly and is not the primary factor degrading performance.

### The Real Reason Context Switches Degrade System Performance

There is another reason why context switches affect performance. This is where a significant performance **difference between process context switches and thread context switches arises.**

The primary difference stems from the fact that processes **have independent virtual address spaces, whereas threads share the same virtual address space.**

#### Process Context Switch
Because processes have independent virtual address spaces, the following performance overheads occur:

- **TLB Flush:** Since the address space has changed, the TLB (Translation Lookaside Buffer. The CPU's internal cache for fast virtual-to-physical address translation must be cleared.
    
- **Cold Cache:** When the new process starts reading memory, the L1/L2 caches contain obsolete data from the previous process. This is known as a cold cache state, forcing the CPU to fetch data directly from the slower main memory (DRAM).

#### Thread Context Switch
When only threads are switched within the same process, they share the address space (Code, Data, Heap), offering the following advantages:
- Since the address space remains the same, **a TLB flush is unnecessary.**
- **The cache data loaded by the previous thread remains valid, maintaining a warm cache state** and allowing execution to continue immediately.

In conclusion, operating systems utilize threads to avoid the performance degradation caused **by TLB flushes and cold caches.**