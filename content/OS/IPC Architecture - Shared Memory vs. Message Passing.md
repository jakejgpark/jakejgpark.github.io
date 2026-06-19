# Message Passing Concept
Message Passing is **a sharing data technique designed for communication between processes whose memories are isolated from each other**, and it is even used when sharing data between threads. What is the reason for this?

## **The Necessity of IPC (Inter-Process Communication)**
Processes each have their own virtual address space. If Process A and Process B need to send and receive data to and from each other, what should they do? There are various IPC methods, but if we choose two representative ones among them, they are as follows:
- Shared Memory
- Message Passing
Because these two have very different design purposes, basically, the two IPCs must be used according to the situation.

### **Shared Memory**
It is used when you want to **exchange a large amount of data fastest without kernel intervention.**

Shared Memory is a method where different processes **cooperate to bypass standard memory isolation policies, granting concurrent access to a designated memory region.**

- **(Advantage) Fastest communication speed:** Because it is at the same level as general memory access, **communication is possible at a very fast speed without kernel intervention during communication/data transfer.** Kernel intervention through system calls is not necessary, and data copying also does not occur.
    
- **(Disadvantage) Synchronization:** Because multiple processes read and write the same memory space at the same time, a Race Condition can occur. To secure Data Integrity, **the developer must directly implement synchronization mechanisms** such as semaphores or [[The Essence of Mutex|mutexes]].

### **Message Passing**
It is used when you want to exchange a small amount of information safely without collision (Thread-Safe). It is especially used for Safety-Critical data.

Without memory sharing, processes send and receive data through `send(message)` and `receive(message)` operations **via a queue or channel provided by the kernel.**

- **(Advantage) Automatic synchronization:** **The OS kernel controls the delivery of messages.** Because of this, it can prevent data access collisions without complex Lock processing like in shared memory.
    
- **(Disadvantage) Performance:**
	The speed is slower compared to Shared Memory.
	- A system call occurs every time a message is sent, requiring a transition to kernel mode.
	- Also, because copying occurs a total of two times as Process A -> Kernel Buffer -> Process B.

## **Do threads that share memory regions also use Message Passing?**
Threads share the `Code`, `Heap`, and `Data` regions among themselves. Then you might think wouldn't it be fine to use Shared Memory, but nevertheless, Message Passing is often used even between threads.

However, as the scale of the system grows and becomes complex, if you use [[The Essence of Mutex|mutexes]] or semaphores, there is a risk of falling into a deadlock. Therefore, threads also use Message Passing among themselves.

Furthermore, thread-level message passing is significantly lighter than inter-process communication (IPC). Because threads share the same address space, they can exchange messages without the heavy overhead of OS [[Understanding Context Switch, Processes vs. Threads|context switches]].

Therefore, even if there is a slight performance loss, they are compromising for systemic safety and expansion.