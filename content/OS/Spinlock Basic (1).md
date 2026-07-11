---
title: "Spinlock: (1) Primitive Spinlock, Test-and-Set and CAS (Atomic Operation)"
date: 2026-06-24
tags:
  - Spinlock
  - Concurrency
  - Cache-Coherence
  - Low-Level
description: Discover the fundamental mechanics of spinlocks and why software logic alone cannot prevent race conditions. This article breaks down the primitive Test-and-Set operation and explores how hardware-level Atomic Operations like Compare-And-Swap (CAS) guarantee synchronization.
---
# What's a spinlock?
A spinlock, along with a mutex, is one of the most widely used synchronization primitives, but its underlying philosophy is different.

The biggest difference lies in the **'waiting mechanism'.**
- When a mutex fails to acquire a lock, it immediately **enters the OS kernel's wait queue and puts the thread to sleep (Block).**
- In contrast, **a spinlock continuously attempts to acquire the lock**, wasting CPU cycles in an infinite loop until it succeeds (Busy-waiting).

The spinlock mechanism itself has undergone several evolutionary stages depending on the hardware architecture. To understand its roots, this article will break down the absolute fundamentals of the most primitive, raw form of a spinlock step by step.
## Test-and-Set
What mechanism constitutes `test_and_set`, the core of a spinlock?
Looking at the C++ Standard Library provides the answer.

```cpp
class DummySpinlock {
	private: std::atomic_flag flag = ATOMIC_FLAG_INIT;
	public: void lock() {
	//CPU Busy-waiting until acquiring the lock
	while (flag.test_and_set(std::memory_order_acquire));
	}
```
- The thread assumes it has successfully acquired the lock and escapes the `while` loop only if the return value of `flag.test_and_set()` is `false`. Otherwise, it continuously attempts to acquire it.

- In other words, the core of the `test_and_set` operation is in the **'state change'**. Only the single thread that captures the exact moment of changing the flag from an unlocked (`false`) state to a locked (`true`) state is recognized as having acquired the lock.

If we express this using standard software logic, it looks like the following structure:
```cpp
class MyAtomicFlag {
private:
    bool flag_value = false; // Default is false and unlocked

public:
    bool test_and_set() {
        // Copy old value
        bool old_value = flag_value;
        // Always set the flag to true (locked)
        flag_value = true;
        // Return the old value
        return old_value;
    }
};
```
It is a straightforward logic:
- Copy the current state of the flag (`old_value`),
- Unconditionally overwrite the flag with `true`, and then return the backed-up old value.

However, a critical question arises regarding this seemingly perfect structure.

**What if multiple threads enter simultaneously and execute the `old_value = flag_value` line at the exact same time?**

Multiple threads could end up grabbing `false` as their `old_value`. As a result, all of them would mistakenly believe they are the winner who acquired the lock, leading to a race condition where they get into the critical section simultaneously.

So, how can we process `test_and_set` as a single, indivisible unit without being split in the middle?

This is where we need the hardware's help to overcome software limitations. Enter the **Atomic Operation** supported at the CPU level.

## Atomic Operation
We cannot prevent concurrency issues purely at the software level. In the end, the only way to secure atomicity is to delegate atomicity to the hardware (CPU).

An atomic operation bundles the series of reading and writing to memory into a single, indivisible 'transaction', **forcing only a single thread to execute it at the hardware level.**

Among these atomic operations, the most representative is **CAS (Compare-And-Swap)**. Going beyond the primitive "unconditionally overwrite and see" lock, CAS atomically handles a condition at the hardware level: Only overwrite if the current state matches its expectation.

In pseudo-code, it looks like the following:
```cpp
bool expected = false;
while (!locked.compare_and_swap(lock_value, true)) { 
expected = false;
}
```

- Setting the expected value to `false`, if the actual value of `locked` is indeed `false`, it changes it to `true` and `locked.compare_and_swap` returns `true`.
- At this time, if the actual value differs from the expectation, it returns `false` and iterates through the `while` loop again.

**Atomicity is achieved by having the hardware guarantee this entire lock acquisition process.**

So far, we have studied the most primitive `Test-and-Set`, and we will continue exploring advanced concepts from here.