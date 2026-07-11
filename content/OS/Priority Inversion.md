# Overview
**Priority Inversion** is a phenomenon where a highest-priority task is forced to wait in a blocked state due to contention for a shared resource (Lock), while lower-priority tasks seize the CPU and execute instead.

## **Conditions**
- **TASK 1 (Low Priority):** Lock acquisition is necessary.
- **TASK 2 (Medium Priority):** No lock acquisition is necessary.    
- **TASK 3 (High Priority):** Lock acquisition is necessary.
![[Pasted image 20260603160914.png]]
## Timeline Analysis
- **Initial State:** TASK 1 begins execution and successfully acquires the lock.
- **T_1 (High-Priority Preemption & Block):**
  The high-priority TASK 3 preempts the CPU. However, because it fails to acquire the lock (which is still held by TASK 1), TASK 3 transitions to a blocked (waiting) state.
	
- **T_2 (Medium-Priority Interruption):**
  **(Priority Inversion)** In this situation, the medium-priority TASK 2 preempts TASK 1. Since TASK 2 has a higher priority than TASK 1 and does not require the lock, it seizes the CPU and executes its operations.
    
- **T_3 (Medium Task Completion):**
  After TASK 2 completely finishes its execution, TASK 1 regain the CPU to resume its work.
    
- **T_4 (Final Execution):**
  Once TASK 1 finishes and releases the lock, the highest-priority TASK 3 immediately acquires the lock, preempts the CPU, and finally completes its execution.

## Conclusion
Even though TASK 3 (High) strictly has a higher priority than TASK 2 (Medium), TASK 3 is forced to wait until TASK 2 completely finishes. This is the danger of Priority Inversion.

To resolve this issue, the **Priority Inheritance Protocol** and **Priority Ceiling Protocol** are commonly implemented at the OS level.