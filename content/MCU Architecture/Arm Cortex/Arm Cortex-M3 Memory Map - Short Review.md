---
title: Arm Cortex-M3 Memory Map - Short Review
date: 2026-07-22
tags:
  - ComputerArchitecture
  - MemoryHierarchy
  - Cache
  - CacheMemory
  - MemoryMap
---
I would like to summarize the characteristics of the Arm Cortex-M3 memory map.
# Memory Map
## **Uses a Predefined Memory Map**

- Since the addresses are predefined at the architecture level, **porting and migrating** software to different manufacturers/products is convenient as long as they use the same Arm Cortex-M3 core. (e.g., addresses for the NVIC, MPU, SysTick, and debug modules are identical).

- **Total 4GB Address Space (32-bit):**    
    - Since it is based on a 32-bit architecture, the maximum addressable space is 4GB.
    - **Code Region:** A dedicated space for executing code. It can also store data (Constant data). Instruction fetches use the I-Code bus, while data accesses use the D-Code bus.
    - **SRAM Region:** Primarily used for connecting internal SRAM. It is Executable, meaning it is possible to load and execute code directly from RAM.
        - **Bit-Band Feature:** Maps a 1MB space within the SRAM region to a 32MB **Bit-Band Alias region**, supporting **hardware-level atomic Read-Modify-Write operations** without the need for software routines.
    - **Peripheral Region:** Used for on-chip peripheral registers (UART, SPI, Timer, etc.). Like the SRAM region, it supports Bit-Band operations, but code execution is strictly prohibited (**Execute Never, XN**).
        
## Memory Access Attributes

- **Bufferable:** When the CPU writes data to memory, it places it in the Write Buffer and **immediately executes the next instruction** without waiting for the memory write cycle to complete.
- **Cacheable:** Data read from memory is stored in the Cache to improve speed for subsequent accesses.
- **Executable:** Determines whether the processor can fetch instructions and **execute code** from the corresponding memory region.
- **Shareable:** Indicates a region that can be shared by multiple bus masters (e.g., CPU, DMA) and where maintaining data coherency is required.