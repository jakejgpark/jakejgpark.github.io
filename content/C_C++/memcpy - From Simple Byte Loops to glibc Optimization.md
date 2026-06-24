

`memcpy` is used to copy a specified number of bytes `n` from one region of memory to another. 

To understand its core mechanics, let's first look at textbook implementation often found in older GCC public domain source code.

## The Raw Implementation (GCC Public Domain)

```c
/* Public domain.  */
#include <stddef.h>

void *
memcpy (void *dest, const void *src, size_t len)
{
  char *d = dest;
  const char *s = src;
  while (len--)
    *d++ = *s++;
  return dest;
}
```

This version is simple. It simply utilizes a finite `while` loop, incrementing 1-byte pointers (`char *`) to copy values in sequence from the source address to the destination address based on the provided length (`len`).

## The Performance Problem of the Raw Implementation
In modern CPU, 32-bit or 64-bit CPU do not access RAM in single bytes. They fetch word size of the data (32-bit or 64-bit). Because the previous `the raw implementation` accesses only a single byte per iteration, it underutilizes the CPU's hardware bandwidth(32-bit or 64-bit), leading to loop overhead and wasted CPU cycles.

## How Modern glibc handles memcpy
To optimize the performance of the hardware, the GNU C Library (`glibc`) abandons the simple byte-loop and uses the different copying mechanism.

Let's consider 64-bit CPU in this example. (Word is a 8 bytes)
- Step 1. Address Alignment: Copies stub bytes one by one until the destination reaches an 8-byte boundary to avoid unaligned hardware penalties.
- Step 2. Page-Level Optimization: Occurs rarely, only when data exceeds tens of MBs and page offsets match perfectly; it alters MMU page tables to achieve zero-copy speed by updating virtual address mappings instead of moving raw bytes.
- **Step 3. Word-Level Block Copy: Moves data 8 bytes at a time using the full data bus width.**
- Step 4. Tail Copy: Invokes a final byte-to-byte copying routine to clean up the remaining 1 to 7 bytes left over at the end.
Step 3 is the most important and frequent step to be used and shows a better optimization than `the raw implementation`.

## Note
Note: While `memcpy` is widely used, it cannot copy correctly if src and dest overlap. To resolve this, it is recommended to use `memmove`.