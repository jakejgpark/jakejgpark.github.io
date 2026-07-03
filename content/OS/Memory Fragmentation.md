The biggest problem when using the Heap is Fragmentation.
Fragmentation can be divided into internal fragmentation and external fragmentation.

## Internal Fragmentation
It occurs when there is a difference **between the requested bytes and the allocated block size.**

```cpp
int* a = (int*)malloc(5 * sizeof(int));
```

Let's analyze the case above.
- **20 bytes** are required, which is 4 bytes of `int` multiplied by 5.
- If the heap allocation is aligned in 16-byte units in the system, **a size of 32 bytes is allocated.**
Accordingly, padding occurs for the remaining 12 bytes, excluding the required 20 bytes.
In other words, internal fragmentation is a memory waste **caused by the hardware's memory alignment rules.**

## External Fragmentation
The fragmentation of empty spaces, created as the process of allocating with `malloc` and deallocating with `free` repeats, becomes a problem.

```cpp
int* a = (int*)malloc(5 * sizeof(int)); // 32 byte allocation
int* b = (int*)malloc(5 * sizeof(int)); // 32 byte allocation
int* c = (int*)malloc(5 * sizeof(int)); // 32 byte allocation

free(b); // b is released
```

If `b` is released as above, **the heap memory state becomes a form with a hole punched in the middle**, as follows:
```
[Block A: Used (32B)] | [Empty (B) : Released (32B)] | [Block C: Used (32B)]
```

Let's assume that 48 bytes are requested at this time.
- Although an empty space (Block B) remains in the middle of the heap, its size is only 32 bytes,
- Then the allocation is skipped, and this space continues to remain as an unusable hole.

If 100 of these 32-byte empty spaces are scattered throughout the entire heap, the total empty space amounts to 3,200 bytes. However, because there is no single massive chunk capable of holding a contiguous 48 bytes, the 48-byte allocation request fails.

Like this, it is a phenomenon where the total memory capacity is sufficient, but a large contiguous block cannot be provided because it is fragmented.