Given the application memory address `Addr`, the address of the shadow byte is computed as: 

(Addr $\gg$ 3) + Offset 

The value of offset is chosen statically during the startup of the program. The value of offset should be chosen in such a way that the region from the `offset` to `offset + max/8` is not occupied at startup. 

On a typical 32-bit Linux or MacOS system, where the virtual address space is 
`0x00000000 - 0xffffffff`  we use offset = 0x20000000 . 

On a typical 64-bit system with 47 significant address bits we use offset = 0x0000100000000000. In some cases (eg. `-fPIE/-pie` compiler flags) a zero offset can be used to simplify instrumentation ever further.   

**TO ADD MORE** 
