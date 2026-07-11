---
title: An At-a-Glance Guide to Memory Hierarchy
date: 2026-07-11
tags:
  - ComputerArchitecture
  - MemoryHierarchy
  - Cache
  - CacheMemory
---

In a computer system, **Memory** plays a crucial role in supplying data so the CPU can work continuously without stalling.
- Data roughly flows in the order of **Registers ➔ Cache ➔ Main Memory (RAM) ➔ Disk** The difference in speed and bandwidth between each level is large.
- The goal of the memory hierarchy is to match the CPU's processing speed by keeping data within the ultra-fast "Cache" zone as much as possible (**Cache Hit**).

![[Pasted image 20260711104110.png]]
### 1. Registers (L0)
- **Data Unit:** Word size
- **Characteristics**:
	- Built using Flip-Flop and Latch circuits. It is the **fastest** memory in the system.
	- Accessible in just 1 clock cycle.
### 2. Cache Memory (L1 - L3)
- **Data Unit:** Cache Line
- **Characteristics:** Constructed with **SRAM** components. Caches have a hierarchical structure based on physical distance from the CPU core.
- **L1 Cache (Level 1)**
    - **Data Structure:** Retrieves cache lines from the L2 cache.
    - **Sharing Structure:** **Dedicated** — Each core has its own fully independent, exclusive L1 cache built inside. (Typically split into L1-I for instructions and L1-D for data).
    - **Approx. Size:** Around **32KB to 64KB** per core. Very small, but exceptionally fast.
        
- **L2 Cache (Level 2)**
    - **Data Structure:** Retrieves cache lines from the L3 cache.
    - **Sharing Structure:** **Cluster Shared** — In modern architectures, it is either dedicated to a single core or shared among a small group of cores (usually clusters of 4).
    - **Approx. Size:** Around **256KB to 1MB** per core (or several MBs per cluster).
        
- **L3 Cache (Level 3)**
    - **Data Structure:** Retrieves cache lines from Main Memory (DRAM).
    - **Sharing Structure:** **Fully Shared** — A massive shared buffer utilized by all cores within the CPU chip. (Also known as LLC or Last Level Cache).
    - **Approx. Size:** **Tens to hundreds of MBs** across the entire CPU (for high-performance processors).
### 3. Main Memory (L4)
- **Data Unit:** Disk Block
- **Characteristics:** This is what we commonly refer to as "RAM" when discussing PC specs.
- **Material:** Uses **DRAM** components, which is lower to access than SRAM.
- **Approx. Size:** Generally **8GB to 64GB+** for personal PCs, vastly larger than caches.

### 4. Local Disk (Local Secondary Storage / L5)
- **Data Unit:** Files
- **Characteristics:** These are secondary storage devices like SSDs or HDDs.
### 5. Remote Secondary Storage (L6)
- **Characteristics:** Refers to Distributed File Systems (DFS), Web Servers, Cloud Storage, etc.

### Conclusion
As a software developer, understanding this memory hierarchy and optimizing your code to maximize **Locality** by ensuring data rarely escapes the cache is one of the most important topic of program optimization.

### References
* Randal E. Bryant, David R. O'Hallaron, *Computer Systems: A Programmer's Perspective (3rd Edition)*, Pearson

