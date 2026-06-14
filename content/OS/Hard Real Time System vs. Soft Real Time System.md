# Overview
This article compares Hard Real-Time Systems and Soft Real-Time Systems. As the terminology suggests, the most critical aspect is how time constraints are handled.

## **Hard Real-Time**
A Hard Real-Time System is a system where the execution results **must be produced within a specified time constraint.**
- Safety-critical systems, which must strictly adhere to time constraints.
- For example, safety-critical systems like brake control systems are typical applications that require hard real-time capabilities.

## Soft real time
A Soft Real-Time System is one where meeting the specified time constraint is ideal, but failing to do so **does not lead to catastrophic consequences.**
- Its strength lies in its **flexibility regarding time constraints.**
- Video streaming is a typical example. Even if the video buffers or drops frames momentarily, it generally does not result in a major problem.
## **Implementation**
- **Linux:** The standard Linux kernel we commonly know supports Soft Real-Time. If you want to achieve Hard Real-Time in Linux, you must apply specific kernel patches.
- **QNX:** QNX supports both Soft Real-Time and Hard Real-Time. While QNX is inherently designed to guarantee Hard Real-Time, not every program running on it needs to be Hard Real-Time.

## **Handling Dynamic Memory Allocation**

- **Soft Real-Time (Standard Linux):** Allocates dynamic memory using the standard `malloc()`. The OS searches the heap to find an appropriate space for memory allocation. Depending on the level of memory fragmentation, the time required to find an empty block is unpredictable, potentially resulting in a worst-case time complexity of **O(N)**.
- **Hard Real-Time (QNX):** Separated from the use of `malloc()`, special-purpose **Memory Pools** are pre-allocated during the initialization phase. Upon request, it provides a pre-configured block without any search process, thereby guaranteeing a time complexity of **O(1)**.

## **Page Fault Handling & Memory Locking**
From an implementation perspective, the most challenging issue is **Page Faults.** When the page a process tries to access is not in physical RAM but swapped out to the hard disk, the time required for hard disk I/O makes it difficult to guarantee real-time performance.

- **Soft Real-Time:** If physical RAM is insufficient, the system evicts unused pages and retrieves necessary pages back from the disk (swapping). **The system naturally tolerates the latency that occurs in this situation.**
    
- **Hard Real-Time:** Fundamentally blocks any latency caused by hard disk I/O.
    - **Using System Calls:** By using the `mlockall()` system call, the OS kernel forcibly locks the code and data of critical processes directly into physical RAM.
    - **Typed Memory:** Even if a developer forgets to invoke the `mlockall()` system call, special memory allocated for hardware communication via the Typed Memory API is **implicitly locked** into physical RAM by the OS kernel, ensuring that Page Faults never occur.