Shadow memory stores metadata for the memory used by the program. It can contain information such as is it accessible ? Describe it ? 

Shadow memory needs to be fast, robust (portable). Different programs can have different shadow memory implementation example [memcheck of valgrind](Memcheck%20Shadow%20Memory.md)
## ASAN Shadow Memory 

`malloc` returns at least 8 byte aligned bytes. So first k bytes (0 $\le$ k $\le$ 8,  generally 8 bytes) are addressable (usable) while the remaining 8-k bytes are not . Shadow memory uses this fact to keep track of application memory by encoding this state into one single byte of shadow memory. 

0 means that all 8 bytes of the corresponding application memory region are addressable
k (1 $\le$ k $\le$ 7) means that the first k bytes are addressable 
any negative value indicates that entire 8-byte word is un-addressable. 

#### ASAN Shadow memory address calculation formula

Given the application memory address `Addr`, the address of the shadow byte is computed as: 

(Addr $\gg$ 3) + Offset 

The value of offset is chosen statically during the startup of the program. The value of offset should be chosen in such a way that the region from the `offset` to `offset + max/8` is not occupied at startup. 

On a typical 32-bit Linux or MacOS system, where the virtual address space is 
`0x00000000 - 0xffffffff`  we use offset = 0x20000000 . 

On a typical 64-bit system with 47 significant address bits we use offset = 0x0000100000000000. In some cases (eg. `-fPIE/-pie` compiler flags) a zero offset can be used to simplify instrumentation ever further.   

**TO ADD MORE** 

#### Instrumentation 

While working with application memory, ASAN computes the address the address of shadow memory using the [[ASAN Shadow memory address calculation formula]] 

If first checks if corresponding shadow byte is 0 or not. If not zero it will report the error and crash the program. 

```c
ShadowAddr = (Addr >> 3) + Offset; 
if (*ShadowAddr != 0)
	ReportAndCrash(Addr);
```

### Runtime library 

runtime library is used to manage shadow memory. 

Malloc and free functions are replaced with a specialized implementation. The malloc function allocates extra memory, the redzone, around the returned region. The redzones are marked un-addressable or *poisoned*. The larger the redzone, the larger the overflows/underflows will be detected. 

For n regions, we allocate n + 1 redzones, such that the right redzone of one region is typically a left redzone of another region. 

The left redzone is used to store the internal data of the allocator (such as the allocation size, thread ID etc.) Internal data cannot be corrupted because they get detected immediately. 

Free function poisons the entire memory region and puts it into *quarantine*, such that this region will not be allocated by malloc any time soon. The quarantined memory acts as a FIFO queue. 



For global objects, redzones are created at compile time. 