---
title: C/C++ volatile - When Compiler Optimization Works Against You
date: 2026-06-23
tags:
  - volatile
  - Optimization
  - C
  - CPP
---
## What is Volatile?
We're going to take a look at the volatile keyword in C/C++. Just as the word itself suggests, **volatile means something that can keep changing.** In other words, it signals that the value of this variable **can be changed at any time by an external factor.**

So why would we define a variable as "something that keeps changing"? The reason is to prevent problems caused by **compiler optimization.** In short, it tells the compiler: don't assume this variable isn't being modified.

We'll explore this through the following four use cases.
## Case 1 — Dead Code Elimination
```cpp
int count = 0;

void Function(void) {
    count++;
}
```

Every time `Function()` is called, the value of `count` should increment by 1. However, when compiler optimization is enabled (at `-O2` or higher), the compiler reasons as follows:

"The variable `count` only gets incremented and is never printed or used anywhere else. Since it has no effect on the program's output, there's no need to perform a memory read and add operation."

As a result, the compiler leaves only the memory address for `count` and eliminates the CPU instruction that adds 1 entirely — this is called **Dead Code Elimination.** Depending on the optimization level, the variable itself may disappear altogether.

## Case 2 - Failure to Detect Asynchronous Changes via Memory-Mapped I/O
```cpp
int* io_isr = 0x70000000; // The address where Hardware I/O level is mapped, initial level is 1.

while (*io_isr) { // Wait until the level becomes zero.
}
```

The fix is to use: `volatile int* io_isr = (volatile int*) 0x70000000;`

In this case, the state of the pin is changed by external hardware I/O, not by software. However, the compiler only looks inside the while loop and wrongly concludes: "There's no code inside the loop that changes the value `io_isr` points to."

So instead of re-reading from the mapped I/O address on every iteration, **it caches the value read once into a CPU register and repeatedly checks only that cached value.** Even if external hardware changes the memory value to 0, the CPU never notices and falls into an infinite loop.

## Case 3 - Problems Caused by an Interrupt Service Routine

```cpp
bool is_packet_received = false; // No volatile

// ISR when the message is received via SPI
void ISR_SPI_Receive(void) {
    is_packet_received = true;
}

int main(void) {
    while (is_packet_received == false) {
    }
    ProcessPacket();
    return 0;
}

```
The fix is to use: `volatile bool is_packet_received = false;`

Similar to Case 2, this is a situation where a hardware interrupt (ISR) modifies a global variable while the main loop is running.

The developer's intent is for the program to break out of the while loop and process the packet once an ISR signals that a packet has been received. However, a compiler with optimization enabled analyzes the flow inside main independently. Since `is_packet_received` never changes within the while loop, it locks the variable to `false` and the program never makes it to `ProcessPacket()`.

## Case 4 - Multithreading
```cpp
bool is_task_done = false;

void Worker_Thread_A(void) {
    is_task_done = true;
}

void Worker_Thread_B(void) {
    while (is_task_done == false) {
        // waiting for Worker_Thread_A to be done.
    }
    Proceed_Next();
}
```
The compiler cannot predict the timing of context switching in a multithreaded environment. Just like the previous examples, when analyzing `Worker_Thread_B`, it concludes that `is_task_done` never changes within the thread itself and caches false in a register. As a result, even after Thread_A finishes its work, Thread_B never notices and cannot escape the infinite loop.

## Closing
`volatile` is a valuable keyword that prevents excessive caching (optimization) by the compiler, thereby guaranteeing memory visibility. 

However, caution is required in multithreaded environments (Case 4). `volatile` only prevents compiler optimization, not **Race Condition**. In this case, you should not rely solely on `volatile`, but use [[The Essence of Mutex|Mutex]] or `std::atomic` for synchronization.

## Reference
- https://barrgroup.com/blog/how-use-cs-volatile-keyword
- https://docs.kernel.org/process/volatile-considered-harmful.html